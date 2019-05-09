Windows EXEのスタブプログラムを作ってみる
================================================================================

これは2012年に書かれた文書の加筆修正版です。技術情報は当時の環境に準じますので、現在とは異なる場合があります。

スタブプログラムとは
--------------------------------------------------------------------------------

一般的なWindowsアプリケーション(.exe)には、
その先頭部分にスタブプログラムと呼ばれるMS-DOSで実行した際にエラーメッセージを表示させるMZフォーマット(COMフォーマット + MZヘッダ)のプログラムが埋め込まれています。
このプログラムは多くの場合、

.. code-block::

    This program cannot be run in DOS mode.

と出力するもので、Stirling等のバイナリエディタでアプリケーションを開いて見てみると、プログラムの先頭部分にその様な文字列を確認できます。

MS-DOSを持っていなくても32bit環境のWindowsを利用していれば、コマンドプロンプトを起動して ``debug`` コマンドを実行したあとに ``g`` コマンドでデバッグ実行が可能です。
64bit環境でも、 `MS-DOS Player <http://takeda-toshiya.my.coocan.jp>`_ の様なエミュレータもあるので動作させることができます。

今回はこのスタブプログラムを自作してみます。

必要なもの
--------------------------------------------------------------------------------

* `NASM <http://www.nasm.us>`_
* Python (楽に作業したいなら)
* Visual C++
* C++とx86 Assemblerの少しの知識

NASMは2012/08/07現在最新版の2.10.03のWin32版を使用します。Visual C++は2010版です。


MS-DOSアセンブラプログラミング
--------------------------------------------------------------------------------

MS-DOSは16bitなのでそれ用のプログラムを書く必要があります。
32bitのプログラミングと違わない部分もありますが、

1. ワードサイズが16bit
2. アドレッシングが ``[BX, BP] + [SI, DI]`` レジスタに限られる

という制約があります。
また、MS-DOSにおける文字列は ``0`` 終端(ヌルターミネイト)ではなく、 ``$`` 終端であることにも注意です。

NASMでHello, world!プログラムを書くと、次の様になります。

.. code-block:: nasm

    org    0x100
    bits   16

    section .text

    ; 2109 INT 21 - WRITE STRING TO STANDARD OUTPUT
    ; 214C INT 21 - TERMINATE WITH RETURN CODE

    start:
        mov     ah, 9
        mov     dx, hoge
        int     0x21
        mov     ax, 0x4c09
        int     0x21
    hoge:
        db      "Hello, world!", 13, 10, '$'

MS-DOSのアセンブラプログラミングについては、国内では `MS-DOS MASM プログラミング入門 <http://www7.plala.or.jp/keny01/asm/dos/>`_ が詳しいです。
システムコールについては、 `Ralf Browns Interrupt List <http://www.delorie.com/djgpp/doc/rbinter/ix/>`_ が詳しいです。

文字列を出力するシステムコールは ``AH = 0x09, DX = 文字列のアドレス`` にしたあと ``int 0x21`` で実行します。
改行には ``\r\n`` (復帰 + 改行)が必要です。

スタブプログラムでは ``ret`` は使えず、 ``AH = 0x4c, AL = 終了ステータス`` としたあと ``int 0x21`` でプログラムを終了します。

NASMアセンブラでコンパイルすると生成物としてCOMファイルができますが、MZヘッダがついていないのでスタブプログラムとしては利用できません。

.. code-block:: doscon

    > nasm -o a.com a.s

そのため、Pythonの簡単なスクリプトを用意してMZヘッダを追加します。

.. code-block:: python

    import os
    import struct
    import sys

    if len(sys.argv) >= 2:
        name, ext = os.path.splitext(sys.argv[1])
        with open(sys.argv[1], 'rb') as f:
            s = f.read()
            l = len(s) + 64
            t = b'\x4d\x5a' + struct.pack('<H', l % 512)
            t += struct.pack('<H', l // 512 + 1) + b'\x00\x00'
            t += b'\x04\x00\x00\x00\xff\xff\x00\x00'
            t += b'\xb8\x00\x00\x00\x00\x00\x00\x00'
            t += b'\x40\x00\x00\x00\x00\x00\x01\x00'
            t += b'\x00\x00\x00\x00\x00\x00\x00\x00'
            t += b'\x00\x00\x00\x00\x00\x00\x00\x00'
            t += b'\x00\x00\x00\x00\x00\x00\x00\x00'
            t += b'\x00\x00\x00\x00' + struct.pack('<L', l)
            with open(name + '.exe', 'wb') as g:
                g.write(t + s)

COMファイルを与えれば、実行可能なスタブプログラムを出力します。

.. code-block:: doscon

    > python3 mzhdr.py a.com


任意のプログラムにスタブプログラムを埋め込む
--------------------------------------------------------------------------------

Visual C++を起動して、任意のプロジェクトを立ち上げたのち、
``「プロジェクト」→「(プロジェクト名)のプロパティ」→「構成プロパティ」→「リンカー」→「コマンドライン」→「追加オプション」`` に、スタブプログラムを追加するオプションで先ほどのMZヘッダを追加したスタブプログラムへのパスを指定します。

.. code-block::

    /STUB:C:\path\to\a.exe

そしてビルドすると、MS-DOSで動かした際に ``Hello, world!`` と出力するプログラムを生成できます。
