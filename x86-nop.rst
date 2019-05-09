binutilsにおけるx86マルチバイトnop
================================================================================

nop
--------------------------------------------------------------------------------

nopはNo OPerationの略で何もしない命令です。
Intelプロセッサではフラグを書き換えない ``xchg`` 命令を利用した ``xchgl %eax, %eax`` がnopに対応し、
ハードウェアの特別なサポートも存在するとしています。

nopを使えば任意のバイト数をnopで埋める際には消費するクロックサイクルを抑えるために
マルチバイトnopを使う方が良いとされています。

プロセッサによって利用するマルチバイトnopは異なり、
`binutils <https://www.gnu.org/software/binutils/>`_ では ``f32`` , ``f16`` , ``alt`` , ``alt_long`` , ``alt_short`` にグループ分けされたnopが定義されています。


マルチバイトnop
--------------------------------------------------------------------------------

以下にnopテーブルを示します。
この命令を直接書いてもうまく処理されないので、かならず ``.byte`` ディレクティブで埋め込むか、
``.p2align`` を利用したコンパイラの最適化を利用してください。

f32
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::

    1: 90                            nop
    2: 66 90                         xchg %ax, %ax
    3: 8d 76 00                      leal 0(%esi), %esi
    4: 8d 74 26 00                   leal 0(%esi, 1), %esi
    5: 90                            nop
        8d 74 26 00                   leal 0(%esi,1), %esi
    6: 8d b6 00 00 00 00             leal 0L(%esi), %esi
    7: 8d b4 26 00 00 00 00          leal 0L(%esi,1), %esi
    8: 90                            nop
        8d b4 26 00 00 00 00          leal 0L(%esi, 1), %esi
    9: 89 f6                         movl %esi, %esi
        8d bc 27 00 00 00 00          leal 0L(%edi, 1), %edi
    10: 8d 76 00                      leal 0(%esi), %esi
        8d bc 27 00 00 00 00          leal 0L(%edi, 1), %edi
    11: 8d 74 26 00                   leal 0(%esi, 1), %esi
        8d bc 27 00 00 00 00          leal 0L(%edi, 1), %edi
    12: 8d b6 00 00 00 00             leal 0L(%esi), %esi
        8d bf 00 00 00 00             leal 0L(%edi), %edi
    13: 8d b6 00 00 00 00             leal 0L(%esi), %esi
        8d bc 27 00 00 00 00          leal 0L(%edi, 1), %edi
    14: 8d b4 26 00 00 00 00          leal 0L(%esi, 1), %esi
        8d bc 27 00 00 00 00          leal 0L(%edi, 1), %edi

f16
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::

    3: 8d 74 00                      lea 0(%esi), %esi
    4: 8d b4 00 00                   lea 0w(%si), %si
    5: 90                            nop
        8d b4 00 00                   lea 0w(%si), %si
    6: 89 f6                         mov %si, %si
        8d bd 00 00                   lea 0w(%di), %di
    7: 8d 74 00                      lea 0(%si), %si
        8d bd 00 00                   lea 0w(%di), %di
    8: 8d b4 00 00                   lea 0w(%si), %si
        8d bd 00 00                   lea 0w(%di), %di

alt
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::

    3: 0f 1f 00                      nopl 0(%[re]ax)
    4: 0f 1f 40 00                   nopl 0(%[re]ax, %[re]ax, 1)
    5: 0f 1f 44 00 00                nopw 0(%[re]ax, %[re]ax, 1)
    6: 66 0f 1f 44 00 00             nopl 0L(%[re]ax)
    7: 0f 1f 80 00 00 00 00          nopl 0L(%[re]ax, %[re]ax, 1)
    8: 0f 1f
        84 00 00 00 00 00             nopw 0L(%[re]ax, %[re]ax, 1)
    9: 66 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)
    10: 66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)

alt_long
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::

    11: 66                            data16
        66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)
    12: 66                            data16
        66                            data16
        66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)
    13: 66                            data16
        66                            data16
        66                            data16
        66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)
    14: 66                            data16
        66                            data16
        66                            data16
        66                            data16
        66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)
    15: 66                            data16
        66                            data16
        66                            data16
        66                            data16
        66                            data16
        66 2e 0f 1f
        84 00 00 00 00 00             nopw %cs:0L(%[re]ax, %[re]ax, 1)


alt_short
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::

    11: 0f 1f 44 00 00                nopl 0(%[re]ax, %[re]ax, 1)
        66 0f 1f 44 00 00             nopw 0(%[re]ax, %[re]ax, 1)
    12: 66 0f 1f 44 00 00             nopw 0(%[re]ax, %[re]ax, 1)
        66 0f 1f 44 00 00             nopw 0(%[re]ax, %[re]ax, 1)
    13: 66 0f 1f 44 00 00             nopw 0(%[re]ax, %[re]ax, 1)
        0f 1f 80 00 00 00 00          nopl 0L(%[re]ax)
    14: 0f 1f 80 00 00 00 00          nopl 0L(%[re]ax)
        0f 1f 80 00 00 00 00          nopl 0L(%[re]ax)
    15: 0f 1f 80 00 00 00 00          nopl 0L(%[re]ax)
        0f 1f 84 00 00 00 00 00       nopl 0L(%[re]ax, %[re]ax, 1)

プロセッサによるnopの選択
--------------------------------------------------------------------------------

どのプロセッサも1-2バイトnopは ``f32`` のものを使用する。

i386, i486, Pentium, Generic32
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``f32`` のテーブルをそのまま使用する。

PentiumPro, Pentium4, Nocona, Core, Core2, Core i7, Generic64
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

3-10では ``alt`` を使用し、11-15では ``alt_long`` を使用する。
64bit環境では ``data16`` によっていくらでもnopを長くできる。

Athlon, K6, K8, AMDFAM10, BD
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

3-10では ``alt`` を使用し、11-15では ``alt_short`` を使用する。


実際マルチバイトnopは速いのか？
--------------------------------------------------------------------------------

簡単な実験をしました。
環境はFedora16, gcc (GCC) 4.6.3 20120306 (Red Hat 4.6.3-2), Intel Core i7 930 @ 2.80GHz です。

.. code-block:: cpp

    #include <stdio.h>

    asm(
        "N_1:\n"
        "    .byte 0x90, 0x90, 0x90, 0x90, 0x90\n"
        "    .byte 0x90, 0x90, 0x90, 0x90, 0x90\n"
        "    .byte 0x90, 0x90, 0x90, 0x90, 0x90\n"
        "    ret\n"
        "N_15:\n"
        "    .byte 0x66, 0x66, 0x66, 0x66, 0x66\n"
        "    .byte 0x66, 0x2e, 0x0f, 0x1f, 0x84\n"
        "    .byte 0x00, 0x00, 0x00, 0x00, 0x00\n"
        "    ret\n"
    );

    int main(void){
        int i;
        for(i = 0; i != -1; i++)
            putchar(N_1(i));    /* or N_15(i) */
        return 0;
    }

.. code-block:: bash

    $ ./n_1.out > /dev/null
    real    0m59.134s
    user    0m58.787s
    sys     0m0.159s

    $ ./n_15.out > /dev/null
    real    0m50.698s
    user    0m50.375s
    sys     0m0.156s
