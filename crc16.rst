CRC-16について
===========================

CRC-16は誤り検出符号のひとつで、ファイルコンテナやデータ転送においてデータの誤りや破損を検出するためによく使われています。
CRC-16は16bitの結果を出力することから16とついていますが、
単純にCRC-16と言っても多くの種類があり、正しい言葉を使わないと混乱を生じるもとになったりします。


どんな種類があるのか？
---------------------------

`Catalogue of parametrised CRC algorithms with 16bits <http://reveng.sourceforge.net/crc-catalogue/16.htm>`_ によれば、よく知られたCRC-16として以下の29種類があります。

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
* X-25
* XMODEM

さて、あなたのプロジェクトではどれが使われているでしょうか？
もしかしてそんなこと気にせずインターネット上に落ちてた誰かのCRC-16を計算しているっぽいソースコードを流用して使ってたりしてないでしょうか？
29種類もアルゴリズムを挙げましたが、これらのCRC-16アルゴリズムの計算方法はすべて同じと言わずとも共通化できるものです。
各アルゴリズムでなにが違うかといえば、多項式、初期値やデータの入力方法だけです。
CRC RevEngではそれぞれのアルゴリズムに対して、与えるパラメータも併記しています。
例えばCRC-16/CCITT-FALSEはよく使われるアルゴリズムですが

.. code-block::

    width=16 poly=0x1021 init=0xffff refin=false refout=false xorout=0x0000
    check=0x29b1 residue=0x0000 name="CRC-16/CCITT-FALSE"

となっていて、「長さ16、多項式(poly)0x1021、初期値(init)0xffff、反転入力(refin)しない、反転出力
(refout)しない、XOR出力(xorout)0x0000」となっています。このパラメータを使ってデータ列
"123456789"のCRC-16を計算すると、チェックサム(check)が0x29b1になることを意味しています。


実装
---------------------------

`A Painless Guide to CRC Error Detection Algorithms <http://www.ross.net/crc/download/crc_v3.txt>`_ の実装が参考になります。
ただし、古いCで書かれているので現代風に書き直すのであれば、こんな感じになります。
速度を重視したテーブルによる実装もありますが、今回はコードだけの実装に留めておきます。

.. code-block:: c

    #include <stdint.h>

    typedef struct {
        uint16_t poly;
        uint16_t digest;
        int refin;
        int refout;
        uint16_t xorout;
    } CRC16;

    static uint16_t crc16_reflect(uint16_t n, int width) {
        /* widthビットの反転をする */
        int i;
        uint16_t r = 0;
        for(i = 0; i < width; i++)
            r |= ((n >> i) & 1) << (width - i - 1);
        return r;
    }

    void crc16_initialize(
        CRC16 *self,
        uint16_t poly, uint16_t init, int refin, int refout, uint16_t xorout
    ) {
        self->poly = poly;
        self->digest = init;
        self->refin = refin;
        self->refout = refout;
        self->xorout = xorout;
        /* refinが真なら初期値を反転する */
        if(self->refin)
            self->digest = crc16_reflect(self->digest, 16);
    }

    void crc16_update(CRC16 *self, const void *data, size_t size) {
        int i;
        const uint8_t *d = (const uint8_t *)data;
        while(size--) {
            /* refinが真なら入力データを反転する */
            self->digest ^= (self->refin ? crc16_reflect(*d++, 8) : *d++) << 8;
            for(i = 0; i < 8; i++) {
                if(self->digest & 0x8000U)
                    self->digest = (self->digest << 1) ^ self->poly;
                else
                    self->digest <<= 1;
            }
        }
    }

    uint16_t crc16_digest(CRC16 *self) {
        uint16_t r = self->digest;
        /* refoutが真なら結果を反転する */
        if(self->refout)
            r = crc16_reflect(r, 16);
        return r ^ self->xorout;
    }

CRCの計算は汎用的なハッシュアルゴリズムと同様に、データをいくつかに分割して入力することができるという性質を持っています。
一度に連続したデータを入力しきらなくても何回かに分けて順番にデータを入力すれば、連続したデータのCRCを計算していることに等しくなるということです。

すなわち、crc16_initialize()で初期化して、crc16_update()を0回以上呼び出したのち、crc16_digest()を呼び出せばCRC-16チェックサムを得られる様になっています。

この実装では29種類のアルゴリズムのうちほとんどはうまく動作しますが、CRC-16/RIELLO, CRC-16/TMS37157, CRC-Aに関してはinitの値を反転する必要があります。
それを考慮した各アルゴリズムのテーブルも定義すると、

.. code-block:: c

    static const CRC16 crc16_variants[] = {
        {0x8005U, 0x0000U, 1, 1, 0x0000U}, /* ARC */
        {0x1021U, 0x1d0fU, 0, 0, 0x0000U}, /* CRC-16/AUG-CCITT */
        {0x8005U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/BUYPASS */
        {0x1021U, 0xffffU, 0, 0, 0x0000U}, /* CRC-16/CCITT-FALSE */
        {0xc867U, 0xffffU, 0, 0, 0x0000U}, /* CRC-16/CDMA2000 */
        {0x8005U, 0xffffU, 0, 0, 0x0000U}, /* CRC-16/CMS */
        {0x8005U, 0x800dU, 0, 0, 0x0000U}, /* CRC-16/DDS-110 */
        {0x0589U, 0x0000U, 0, 0, 0x0001U}, /* CRC-16/DECT-R */
        {0x0589U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/DECT-X */
        {0x3d65U, 0x0000U, 1, 1, 0xffffU}, /* CRC-16/DNP */
        {0x3d65U, 0x0000U, 0, 0, 0xffffU}, /* CRC-16/EN-13757 */
        {0x1021U, 0xffffU, 0, 0, 0xffffU}, /* CRC-16/GENIBUS */
        {0x1021U, 0x0000U, 0, 0, 0xffffU}, /* CRC-16/GSM */
        {0x6f63U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/LJ1200 */
        {0x8005U, 0x0000U, 1, 1, 0xffffU}, /* CRC-16/MAXIM */
        {0x1021U, 0xffffU, 1, 1, 0x0000U}, /* CRC-16/MCRF4XX */
        {0x5935U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/OPENSAFETY-A */
        {0x755bU, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/OPENSAFETY-B */
        {0x1dcfU, 0xffffU, 0, 0, 0xffffU}, /* CRC-16/PROFIBUS */
        {0x1021U, 0x554dU, 1, 1, 0x0000U}, /* CRC-16/RIELLO (0xb2aa reflected) */
        {0x8bb7U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/T10-DIF */
        {0xa097U, 0x0000U, 0, 0, 0x0000U}, /* CRC-16/TELEDISK */
        {0x1021U, 0x3791U, 1, 1, 0x0000U}, /* CRC-16/TMS37157 (0x89ec reflected) */
        {0x8005U, 0xffffU, 1, 1, 0xffffU}, /* CRC-16/USB */
        {0x1021U, 0x6363U, 1, 1, 0x0000U}, /* CRC-A (0xc6c6 reflected) */
        {0x1021U, 0x0000U, 1, 1, 0x0000U}, /* KERMIT */
        {0x8005U, 0xffffU, 1, 1, 0x0000U}, /* MODBUS */
        {0x1021U, 0xffffU, 1, 1, 0xffffU}, /* X-25 */
        {0x1021U, 0x0000U, 0, 0, 0x0000U}, /* XMODEM */
    };

となります。定数とショートカット関数を用意すれば簡単にCRC-16の各実装を試すことができるようになります。

.. code-block:: c

    #define CRC16_ARC        0
    #define CRC16_AUG_CCITT  1
    :
    #define CRC16_X_25      27
    #define CRC16_XMODEM    28

    int crc16_algorithm(CRC16 *self, int algorithm) {
        const CRC16 *crc16;
        if(algorithm < 0)
            return -1;
        if(algorithm >= sizeof(crc16_variants) / sizeof(crc16_variants[0]))
            return -1;
        crc16 = &crc16_variants[algorithm];
        crc16_initialize(
            self,
            crc16->poly, crc16->digest, crc16->refin, crc16->refout, crc16->xorout
        );
        return 0;
    }
