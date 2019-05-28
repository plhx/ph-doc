CRC-16
===========================

CRC-16は誤り検出符号のひとつで、ファイルコンテナやデータ転送においてデータの誤りや破損を検出するために使われています。
計算アルゴリズムはどのCRC-16も同じものを使用していますが、5つのパラメータである生成多項式(poly), 初期値(init), 反転入力(refin), 反転出力(refout), 排他的論理和出力(xorout)が異なります。

よく使われているパラメータには名前がつけられていて、`Catalogue of parametrised CRC algorithms with 16bits <http://reveng.sourceforge.net/crc-catalogue/16.htm>`_ によれば、よく知られたCRC-16として以下の30種類があります。
ここではリストを簡潔にするためにエイリアス(別名)は省いて掲載しています。

* ARC
* CRC-16/AUG-CCITT
* CRC-16/BUYPASS
* CRC-16/CCITT-FALSE
* CRC-16/CDMA2000
* CRC-16/CMS
* CRC-16/DDS-100
* CRC-16/DECT-R
* CRC-16/DECT-X
* CRC-16/DNP
* CRC-16/EN-13757
* CRC-16/GENIBUS
* CRC-16/GSM
* CRC-16/LJ1200
* CRC-16/MAXIM
* CRC-16/MCRF4XX
* CRC-16/OPENSAFETY-A
* CRC-16/OPENSAFETY-B
* CRC-16/PROFIBUS
* CRC-16/RIELLO
* CRC-16/T10-DIF
* CRC-16/TELEDISK
* CRC-16/TMS37157
* CRC-16/USB
* CRC-A
* KERMIT
* MODBUS
* NRSC_5
* X-25
* XMODEM

あなたのプロジェクトでこのような名前が出てきたならそれは素晴らしいプロジェクトだと思います。
ほとんどの場合は単に「CRC-16をチェックサムとする。生成多項式はこれ」とぐらいしか書かれていません。
しまいにはインターネット上の怪しいソースコードをそのまま実装して終わりです。これが現場です。


CRC-16の計算方法
--------------------------------------------------------------------------------

それではあまりにも雑なので、ここではもう少しそれっぽいことを説明します。
`Catalogue of parametrised CRC algorithms with 16bits <http://reveng.sourceforge.net/crc-catalogue/16.htm>`_ にはそれぞれの名前とパラメータが書かれています。
例えばCRC-16/CCITT-FALSEは

.. code-block::

    width=16 poly=0x1021 init=0xffff refin=false refout=false xorout=0x0000
    check=0x29b1 residue=0x0000 name="CRC-16/CCITT-FALSE"

となっていて、「長さ16、多項式(poly) ``0x1021`` 、初期値(init) ``0xffff`` 、反転入力(refin) ``しない`` 、反転出力
(refout) ``しない`` 、排他的論理和出力(xorout) ``0x0000`` 」となっています。
そしてこのパラメータを使ってバイト列 ``123456789`` のCRC-16を計算すると、チェックサム(check)が ``0x29b1`` になるとしています。

もし、あなたのプロジェクトでどのCRC-16が使われているのかわからなくてもこのチェックサムと突き合わせてみれば、どのパラメータを使用しているかわかります。
今からでも仕様書にCRC-16の名前とパラメータを書きましょう。きっと後世の役に立つはずです。

ではCRC-16の計算方法はというと、これは `A Painless Guide to CRC Error Detection Algorithms <http://www.ross.net/crc/download/crc_v3.txt>`_ の実装が参考になります。
この文献では古いCの実装例が示されていますが、わかりやすく現代風にPythonで書き直すとこうなります。

.. code-block:: python

    class CRC16:
        ARC          = lambda: CRC16(0x8005, 0x0000,  True,  True, 0x0000)
        AUG_CCITT    = lambda: CRC16(0x1021, 0x1d0f, False, False, 0x0000)
        BUYPASS      = lambda: CRC16(0x8005, 0x0000, False, False, 0x0000)
        CCITT_FALSE  = lambda: CRC16(0x1021, 0xffff, False, False, 0x0000)
        CDMA2000     = lambda: CRC16(0xc867, 0xffff, False, False, 0x0000)
        CMS          = lambda: CRC16(0x8005, 0xffff, False, False, 0x0000)
        DDS_110      = lambda: CRC16(0x8005, 0x800d, False, False, 0x0000)
        DECT_R       = lambda: CRC16(0x0589, 0x0000, False, False, 0x0001)
        DECT_X       = lambda: CRC16(0x0589, 0x0000, False, False, 0x0000)
        DNP          = lambda: CRC16(0x3d65, 0x0000,  True,  True, 0xffff)
        EN_13757     = lambda: CRC16(0x3d65, 0x0000, False, False, 0xffff)
        GENIBUS      = lambda: CRC16(0x1021, 0xffff, False, False, 0xffff)
        GSM          = lambda: CRC16(0x1021, 0x0000, False, False, 0xffff)
        LJ1200       = lambda: CRC16(0x6f63, 0x0000, False, False, 0x0000)
        MAXIM        = lambda: CRC16(0x8005, 0x0000,  True,  True, 0xffff)
        MCRF4XX      = lambda: CRC16(0x1021, 0xffff,  True,  True, 0x0000)
        OPENSAFETY_A = lambda: CRC16(0x5935, 0x0000, False, False, 0x0000)
        OPENSAFETY_B = lambda: CRC16(0x755b, 0x0000, False, False, 0x0000)
        PROFIBUS     = lambda: CRC16(0x1dcf, 0xffff, False, False, 0xffff)
        RIELLO       = lambda: CRC16(0x1021, 0x554d,  True,  True, 0x0000)
        T10_DIF      = lambda: CRC16(0x8bb7, 0x0000, False, False, 0x0000)
        TELEDISK     = lambda: CRC16(0xa097, 0x0000, False, False, 0x0000)
        TMS37157     = lambda: CRC16(0x1021, 0x3791,  True,  True, 0x0000)
        USB          = lambda: CRC16(0x8005, 0xffff,  True,  True, 0xffff)
        CRC_A        = lambda: CRC16(0x1021, 0x6363,  True,  True, 0x0000)
        KERMIT       = lambda: CRC16(0x1021, 0x0000,  True,  True, 0x0000)
        MODBUS       = lambda: CRC16(0x8005, 0xffff,  True,  True, 0x0000)
        NRSC_5       = lambda: CRC16(0x080b, 0xffff,  True,  True, 0x0000)
        X_25         = lambda: CRC16(0x1021, 0xffff,  True,  True, 0xffff)
        XMODEM       = lambda: CRC16(0x1021, 0x0000, False, False, 0x0000)

        def __init__(self, poly, init, refin, refout, xorout):
            self.poly = poly
            self.init = init
            self.refin = refin
            self.refout = refout
            self.xorout = xorout
            if self.refin:
                self.init = self.__reflect(init, 16)

        def __reflect(self, x, bits):
            r = 0
            for i in range(bits):
                r = (r << 1) | ((x >> i) & 1)
            return r

        def update(self, data):
            for x in data:
                self.init ^= (self.__reflect(x, 8) if self.refin else x) << 8
                for _ in range(8):
                    if self.init & 0x8000:
                        self.init = ((self.init << 1) ^ self.poly) & 0xffff
                    else:
                        self.init = (self.init << 1) & 0xffff

        def digest(self, data=b''):
            self.update(data)
            x = self.init
            if self.refout:
                x = self.__reflect(x, 16)
            return x ^ self.xorout

CRCの計算は汎用的なハッシュアルゴリズムと同様に、データをいくつかに分割して入力することができるという性質を持っています。
例えば、ネットワークの仕様上一度に多くのデータを送信できない場合であっても、チェックサムを計算できるということです。

このPythonコードで言えば、 ``CRC16`` のインスタンスを作り、 ``update()`` を0回以上呼び出したのち、 ``digest()`` を呼び出せばCRC-16のチェックサムを得られる様になっています。
