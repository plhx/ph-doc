NetworkStream#DataAvailableは使ってはいけない
================================================================================

C#の `NetworkStream.DataAvailable <https://docs.microsoft.com/ja-jp/dotnet/api/system.net.sockets.networkstream.dataavailable?view=netframework-4.8>`_ は、 ``TcpClient#GetStream()`` などから得られるストリームにデータがあるかどうかを確認するプロパティです。
MSDNの公式リファレンスにはサンプルコードが載っていますが、これをこのまま鵜呑みにして使用してはいけません。

.. code-block:: csharp

    // 重要な箇所だけを抜粋
    // Incoming message may be larger than the buffer size.
    do{
        numberOfBytesRead = myNetworkStream.Read(myReadBuffer, 0, myReadBuffer.Length);
        myCompleteMessage.AppendFormat("{0}", Encoding.ASCII.GetString(myReadBuffer, 0, numberOfBytesRead));
    }
    while(myNetworkStream.DataAvailable);


コードの説明
--------------------------------------------------------------------------------

`NetworkStream#Read(Byte[], Int32, Int32) <https://docs.microsoft.com/ja-jp/dotnet/api/system.net.sockets.networkstream.read?view=netframework-4.8>`_ はノンブロッキングでバイト列を読み取ってバッファに格納するメソッドです。
返り値は0以上の数で、ソケットが閉じられている場合と受信データがない場合は0を返します。
`NetworkStream#DataAvailable <https://docs.microsoft.com/ja-jp/dotnet/api/system.net.sockets.networkstream.dataavailable?view=netframework-4.8>`_ は受信データがあるならtrue、ないならfalseを返します。

一見良さそうなコードに見えますが、次の問題があります。


TCPパケットは分割されうる
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ひとつのTCPパケットで期待している全データを受信できる保証はありません。
例えば、64バイトのデータを送信する場合に、ふたつのパケットに分割されて、1回目に2バイト、2回目に62バイト送られることもありえます。
その挙動を示すのが次のコードです。WindowsでもMac OS Xでも最初に2バイト送信して、次のパケットで62バイト送信します。

.. code-block:: none

    using System;
    using System.IO;
    using System.Net;
    using System.Net.Sockets;
    using System.Threading;


    class MainClass {
        public static void Main(String[] args) {
            // クライアント接続のスレッド
            Thread thread = new Thread(() => {
                Thread.Sleep(3000);
                using(TcpClient client = new TcpClient("127.0.0.1", 50000))
                using(BinaryWriter bw = new BinaryWriter(client.GetStream())) {
                    bw.Write((UInt16)0xffff);
                    // 擬似的なネットワーク遅延
                    // Thread.Sleep(100);
                    bw.Write(new Byte[62]);
                    bw.Flush();
                }
            });
            thread.IsBackground = true;
            thread.Start();

            // ローカルアドレスで待ち受け
            TcpListener server = new TcpListener(IPAddress.Any, 50000);
            server.Start();
            while(true) {
                int bytesRead = 0;
                Byte[] buffer = new Byte[1024];
                TcpClient client = server.AcceptTcpClient();
                NetworkStream stream = client.GetStream();
                do {
                    bytesRead += stream.Read(buffer, bytesRead, buffer.Length - bytesRead);
                }
                while(stream.DataAvailable);
                System.Diagnostics.Debug.WriteLine($"bytesRead = {bytesRead}");
            }
        }
    }

このコードは、ローカルアドレスの50000ポートにTCPサーバを建て、別スレッドから3秒後にTCPクライアントで接続します。
このプログラムの出力は、

.. code-block:: none

    bytesRead = 2

となり、期待した64バイトの受信にはならないことがあります。
擬似的なネットワークの遅延として `Thread.Sleep() <https://docs.microsoft.com/ja-jp/dotnet/api/system.threading.thread.sleep?view=netframework-4.8>`_ を入れて値を大きくしていくと、確かにうまくいかないことが確認できます。


一度に全データを送ればいいのでは？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ストリームにその都度出力せず一回の処理で全データを送信すれば、少ない数のパケットで送られますが、
ライブラリのバッファリングの仕様によるところもありますし、
そもそも送るデータが大きい場合は、イーサネットフレームに収めるために分割されるので、根本的な解決方法ではありません。
分割されたパケットが到着する間に受信処理をすれば、0バイト受信の可能性もあります。


ではどうすればいいのか？
--------------------------------------------------------------------------------

タイムアウトとブロッキング処理を組み合わせるべきです。
``ReceiveTimeout`` プロパティにプロトコルで定めるところのタイムアウト値を設定した上で、
`BinaryReader <https://docs.microsoft.com/ja-jp/dotnet/api/system.io.binaryreader?view=netframework-4.8>`_ のようなブロッキング読み込みをするクラスを使って、ちょうど必要な分だけデータを読み取るべきです。

