x86 汎用命令のModR/Mバイト
================================================================================

x86の汎用命令はオペコードに続き、場合によってはオペランドを決定するModR/Mバイトが付加されます。
ModR/Mバイトは``mod`` , ``reg/opcode`` , ``r/m`` フィールドから構成され、

* ``mod`` フィールドは ``r/m`` のアドレス指定モードを決定
* ``reg/opcode`` フィールドはオペランドのうち、レジスタ側を決定
* ``r/m`` フィールドはオペランドのうち、レジスタ/メモリ側を決定

します。
また、``mod`` , ``r/m`` フィールドの指定によっては追加のSIBバイトとディスプレースメントバイト(1または4バイト)がつくことがあります。

図示すると、おおよそ以下の様になります。

.. code-block::

    mod   reg   r/m   scale index base     disp
    +----+-----+-----+ +----+-----+-----+ +-------
    | XX | XXX | XXX | | XX | XXX | XXX | | ...
    +----+-----+-----+ +----+-----+-----+ +-------

reg/opcode, r/m, base, indexフィールドのレジスタ指定
--------------------------------------------------------------------------------

``reg/opcode`` , ``r/m`` , ``base`` , ``index`` フィールドのビットに対するレジスタは次の様になります。

.. code-block::

            reg     r/m        base    index
    +-----+--------+--------+ +-------+--------+
    | 000 | %eax   | %eax   | | %eax  | %eax   |
    | 001 | %ecx   | %ecx   | | %ecx  | %ecx   |
    | 010 | %edx   | %edx   | | %edx  | %edx   |
    | 011 | %ebx   | %ebx   | | %ebx  | %ebx   |
    | 100 | %esp   | %esp*2 | | %esp  | %eiz*4 |
    | 101 | %ebp*1 | %ebp   | |    *3 | %ebp   |
    | 110 | %esi   | %esi   | | %esi  | %esi   |
    | 111 | %edi   | %edi   | | %edi  | %edi   |
    +-----+--------+--------+ +-------+--------+

1. ``mod = 0b00`` のときは、``%ebp`` レジスタの代わりに ``disp32`` (ディスプレースメントだけ)になります。
2. ``mod = 0b11`` 以外のとき、``%esp`` レジスタ/メモリの代わりにSIBバイトで指定したレジスタ/メモリになります。
3. ``%ebp`` レジスタの代わりに指定なしになり、ベースが ``0`` として扱われます。
4. ``%esp`` レジスタの代わりにゼロレジスタである ``%eiz`` になり、インデックスが ``0`` として扱われます。

指定モード
--------------------------------------------------------------------------------

レジスタ-レジスタ指定 (mod = 0b11)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``mod = 0b11`` のときはソースもデスティネーションもレジスタになります。
``r/m = 0b101`` のときはSIBバイトが付加されますが、 ``mod = 0b11`` はそれに優先します。

.. code-block::

    mod   reg   r/m
    +----+-----+-----+
    | 11 | 000 | 000 | -> %eax, %eax
    | 11 | 000 | 100 | -> %eax, %esp
    +----+-----+-----+

レジスタ-メモリ指定 (mod = 0b00, 0b01, 0b10 | r/m != 0b100)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

このときは、 ``r/m`` または ``base`` フィールドで指定したレジスタにディスプレースメントを加えた実効アドレス指定になります。

* ``mod = 0b00`` のときは、ディスプレースメントなし
    * ただし、 ``r/m = 0b101`` のときは、32bitディスプレースメントのみを指定
* ``mod = 0b01`` のときは、8bitディスプレースメント
* ``mod = 0b10`` のときは、32bitディスプレースメント

.. code-block::

    mod   reg   r/m
    +----+-----+-----+
    | 00 | 000 | 000 |       -> %eax, (%eax)
    | 01 | 000 | 000 | +(1B) -> %eax, disp8(%eax)
    | 10 | 000 | 000 | +(4B) -> %eax, disp32(%eax)
    | 00 | 000 | 101 | +(4B) -> %eax, disp32
    | 01 | 000 | 101 | +(1B) -> %eax, disp8(%ebp)
    | 10 | 000 | 101 | +(4B) -> %eax, disp32(%ebp)
    +----+-----+-----+

レジスタ-メモリ指定2 (mod = 0b00, 0b01, 0b10 | r/m = 0b100)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``r/m = 0b100`` のときは、 ``%esp`` レジスタの代わりにSIBバイトで ``base + index * scale`` のアドレス指定になります。

スケールの値は``scale`` フィールドで指定されている値に対して``1 << scale`` で求められます。

.. code-block::

    mod   reg   r/m   scale index base
    +----+-----+-----+ +----+-----+-----+
    | 00 | 000 | 100 | | 00 | 000 | 000 |       -> %eax, (%eax, %eax, 1)
    | 00 | 000 | 100 | | 01 | 000 | 100 |       -> %eax, (%esp, %eax, 2)
    | 00 | 000 | 100 | | 10 | 000 | 101 | +(4B) -> %eax, disp32(, %eax, 4)
    | 00 | 000 | 100 | | 00 | 100 | 101 | +(4B) -> %eax, disp32(, %eiz, 1)
    | 01 | 000 | 100 | | 11 | 000 | 101 | +(1B) -> %eax, disp8(%ebp, %eax, 8)
    | 00 | 000 | 100 | | 00 | 100 | 000 |       -> %eax, (%eax, %eiz, 1)
    | 00 | 000 | 100 | | 00 | 101 | 000 |       -> %eax, (%eax, %ebp, 1)
    | 01 | 000 | 100 | | 00 | 101 | 101 | +(1B) -> %eax, disp8(%ebp, %ebp, 1)
    +----+-----+-----+ +----+-----+-----+

* オペランド ``%eax, (%esp)`` の様に ``%esp`` がアドレス指定だとSIBバイトでの指定が必須になります
    * ``0b00000100, 0b00100100`` = ``%eax, (%esp, %eiz, 1)`` = ``%eax, (%esp)``
* ``r/m = 0b101`` と同じ扱いが ``base`` フィールドにも適用されます
    * ``base = 0b101`` のとき、ベースはなし( ``0`` 扱い)になり32bitディスプレースメントを指定します
* SIBバイトあり・なしで動作が変わらないオペコードを生成することもできます
    * ``0b00000001`` = ``%eax, (%ecx)``
    * ``0b00000100, 0b00100001`` = ``%eax, (%ecx, %eiz, 1)``

オペランドを出力するプログラム例
--------------------------------------------------------------------------------

.. code-block:: python

    REG = ['%eax', '%ecx', '%edx', '%ebx', '%esp', '%ebp', '%esi', '%edi']

    def print_operand(code):
        mrm = code[0]
        mod, reg, mem = (mrm >> 6) & 0x03, (mrm >> 3) & 0x07, (mrm >> 0) & 0x07
        if mod == 0b11:
            return REG[reg], REG[mem]
        is_sib = mem == 0b100
        is_a32 = mod == 0b00 and mem == 0b101
        dst = '' if is_a32 else REG[mem]
        if is_sib:
            sib = code[1]
            scl, idx, bas = (sib >> 6) & 0x03, (sib >> 3) & 0x07, (sib >> 0) & 0x07
            is_a32 = mod == 0b00 and bas == 0b101
            dst = '{}, {}, {}'.format(
                '' if is_a32 else REG[bas],
                '%eiz' if idx == 0b100 else REG[idx],
                1 << scl
            )
        dbt = 4 if is_a32 or mod == 0b10 else mod
        dsp = int.from_bytes(code[is_sib + 1:is_sib + dbt + 1], byteorder='little')
        if dst:
            dst = '(' + dst + ')'
        return REG[reg], ('0x{:0{}x}'.format(dsp, dbt) if dbt else '') + dst

    print(print_operand(bytes([0b11000000])))
    print(print_operand(bytes([0b11000100])))

    print(print_operand(bytes([0b00000000])))
    print(print_operand(bytes([0b01000000, 0xff])))
    print(print_operand(bytes([0b10000000, 0xef, 0xbe, 0xad, 0xde])))
    print(print_operand(bytes([0b00000101, 0xef, 0xbe, 0xad, 0xde])))
    print(print_operand(bytes([0b01000101, 0xff])))
    print(print_operand(bytes([0b10000101, 0xef, 0xbe, 0xad, 0xde])))

    print(print_operand(bytes([0b00000100, 0b00000000])))
    print(print_operand(bytes([0b00000100, 0b01000100])))
    print(print_operand(bytes([0b00000100, 0b10000101, 0xef, 0xbe, 0xad, 0xde])))
    print(print_operand(bytes([0b00000100, 0b00100101, 0xef, 0xbe, 0xad, 0xde])))
    print(print_operand(bytes([0b01000100, 0b11000101, 0xff])))
    print(print_operand(bytes([0b00000100, 0b00100000])))
    print(print_operand(bytes([0b00000100, 0b00101000])))
    print(print_operand(bytes([0b01000100, 0b00101101, 0xff])))
