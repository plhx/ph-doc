GNU Smalltalk 入門
================================================================================

Smalltalkとはなんぞや？
--------------------------------------------------------------------------------

Aran Kayによって設計されたオブジェクト指向言語です。
C++の抽象データ型としてのオブジェクト指向と違う側面からオブジェクト指向にアプローチしており、
オブジェクト間のコミュニケーション、Smalltalkではメッセージングと呼ばれていますが、
このメッセージングによるプログラミングスタイルを採用しています。

メッセージングによるプログラミングでは、
オブジェクトがメッセージを受けるとそのメッセージによってオブジェクトがその中身のデータ構造や実装はともかく、
受けたメッセージに対する処理して結果を返してくれます。実質的にC++のメソッド呼び出しと変わりませんが、
C++のメソッド呼び出しはオブジェクトが「する」のに対して、
Smalltalkのメッセージングではオブジェクトに「させる」ようなイメージを持ちます。
実際、Smalltalkではオブジェクトとメソッド呼び出しは切り離すことができます。

Smalltalkはパワフルな機能を持ちながら、陽の目を見ることはありませんでした。
それというのもプログラミング言語の歴史上あまりにも早く出すぎた上に(Cが1972年でSmalltalk-72と同時期)、
パワフル過ぎた言語仕様のため、当時の計算機では十分にその能力を生かすことができなかったのが一因です。
今日ではSmalltalkの流れを受け継いだ言語としてAppleによるObjective-Cや、
似た思想を持つ日本産のオブジェクト指向言語Rubyがあり、
Objective-Cは主にiOS向けアプリケーションの開発に用いられ、
RubyはWebアプリケーションフレームワークRuby on Railsで一定の地位を確立しました。

現代のSmalltalkはというと、Smalltalk-80と呼ばれる仕様に沿った処理系がいくつかあり、
昔から人気で今でも開発が続いているオープンソースのSqueakとPharo、
エンタープライズ向けの正統派であるCincom社のVisualWorks、JavaScript実装のAmber、
CUI特化の変り種であるGNUプロジェクト下のGNU Smalltalkなどが有名です。
Smalltalkはオブジェクト指向言語として名前だけは知られていますが、ユーザは少なく、
巷では化石になった言語とも言われていますが、そうとも言い切れず、
国内でSmalltalkを採用して一定の成功をおさめた数少ない企業としてSORABITOが挙げられます。
SORABITOはALLSTOCKERというブランド名で建機のオンラインマーケットを運営していて、その基盤をPharoで構築しています。
優秀なSmalltalkエンジニアを全国から採用しており、自社製品用にSmalltalkでライブラリを設計するなど、
かなりの熱意を持ってSmalltalk市場の開拓を行なっています。


Smalltalkを学ぶのは難しい？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

結論を先に言えば簡単です。Smalltalkで覚えるべき構文はとても少なく、
基本的にオブジェクトに対するメッセージング(メソッド呼び出し)の方法とオブジェクトリテラルの書き方さえ覚えてしまえば、
基本的なことはほとんど理解したと言えます。
ただし、Smalltalkには200を超える組み込みクラスやオブジェクトがありますので、全部を覚える必要はありませんが、
クラスリファレンスを読むことになるでしょう。でもこれはどの言語にも言えることです。

Smalltalkのよさのひとつには、特別扱いが少なくストレスフリーなプログラミングができるというものがあります。
すべてのデータがオブジェクトで、プリミティブ型が混在していたりということがなく、プログラマに低水準なことをあまり考えさせません。
オブジェクトはなんらかのクラスを継承していて、
スーパークラスで定義されたメソッドはサブクラスでも同じ動作や似た動作をするようになっており、
スーパークラスがなにであるか分かれば動作の把握も容易で、多態性の利点を得つつ、言語自体の学習コストも低く保てています。

しかしながら、Smalltalkには日本語の文献が少ないので少なからず英語力が試されます。
Smalltalkを書くような人は英語が読めてしまうし、内容も理解してしまうので日本語訳を出す必要がない、
そして多くの処理系では処理系自身でクラスリファレンスを参照できるので文献を書く必要がないというのがあるかと思います。


Smalltalkは使えるのか？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

どの処理系を使うかに大きく依存します。処理系のひとつPharoは今の時代に必要とされるライブラリも充実していて、
実用に耐えうる処理系と言えます。Windows, Mac OS X, Linux対応というサポートの広さもあります。
もしガッツリSmalltalkを使っていこうという気があるのであれば、Pharoの使用を第一に検討してみても良いでしょう。
Agile Visualizationのような統計・解析向けの亜種も多数作られています。

本稿のGNU Smalltalkはほかの処理系と比較してよりコンパクトかつシンプルな設計なので、
Pharoのような処理系ではあらかじめ用意されていて簡単にできることができないことが多く、自分で実装しなければならないことが多いです。
昨今人気でライブラリも充実しているPythonやRubyと同じ感覚では使えません(これらがあまりにも色々できすぎるのもあります)。

速度面で言えば、Smalltalkはそれほど速い動作をしません。処理系は入力されたソースコードをバイトコードに変換して、
仮想スタックマシンで実行する方式を採用していますし、メッセージングでの多重ディスパッチがオーバーヘッドを高めています。
速度が必要な用途ではFFIによる共有ライブラリ呼び出しなどを使っていくことになるでしょう。


GNU Smalltalkを試してみる
--------------------------------------------------------------------------------

GNU SmalltalkはSmalltalk-80の一実装です。
Smalltalkは基本的にシステムブラウザと呼ばれる統合開発環境を兼ねたGUI操作ウィンドウを持ちますが(JavaScript実装のAmberでさえも！)、
GNU SmalltalkはLinuxのCUI向けの実装なので、すべてコンソールで完結できるある意味とても清々しい処理系です。
コンソールで実行できるので、EmacsやVimで手軽に編集できるのも良い点です。

推奨する実行環境はLinuxですが、Mac OS X向けにHomebrewでインストールもできますし、
有志によってWindows向けのバイナリも配布されています。
なお、Mac OS X向けでは浮動小数点数演算の一部に問題があり、意図せず例外を送出する不具合があります。


インストール
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GNU Smalltalkのインストールは簡単です。
もし次の方法でインストールできなかったらソースコードからビルドすることになりますが、特に難しくはありません。
Raspberry Piなどの特殊な環境ではソースコードからビルドする必要があります。
その際、依存するライブラリがいくつかあるのでビルドエラーが発生したら足りてないライブラリが無いか確かめてみてください。

Linux (RedHat系)

.. code-block:: bash

    # yum install gnu-smalltalk

Linux (Debian系)

.. code-block:: bash

    # apt-get install gnu-smalltalk

Mac OS X

.. code-block:: bash

    $ brew install gnu-smalltalk

Windows

`https://github.com/mcandre/gst-win <https://github.com/mcandre/gst-win>`_ からダウンロードできます。

環境変数PATHにgst.exeへのパスを追加する必要があります。


起動してみる
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GNU Smalltalkを起動するにはgstを実行します。
起動すると、REPL(Read-Eval-Print-Loop)の対話型インタフェースが立ち上がります。

.. code-block:: bash

    $ gst
    GNU Smalltalk ready
    st>

``st>`` はユーザに入力をうながすプロンプトです。
この状態では逐次的にSmalltalkのコードを実行できます。
複数行にわたるコードは処理系が適切に判断して、実行単位ごとに処理するので、無理に1行に収める必要はありません。
電卓がわりに試しにいくつか計算をしてみます。好きな数式を打ち込んで改行すると、コードが実行されます。
もしgstを終了したくなったらUNIX環境ではCtrl-D、Windows環境ではCtrl-Zを空行で入力します。

.. code-block:: bash

    st> 100
    100
    st> 2 + 3
    5
    st> -7 * 2
    -14
    st> 42.0 +
    st> 11.5
    53.5

数値だけでなく、適当な文字列を表示することもできます。シングルクォートで囲むと文字列として扱われます。
間違えてダブルクォートを使うとSmalltalkではコメントになってしまうので気をつけてください。

.. code-block:: bash

    st> 'Hello, world!'
    'Hello, world!'

``'Hello, world!'`` と打ち込むことで、そのとおり表示はされましたが、
これはREPLで ``'Hello, world!'`` を評価した値をそのまま表示しているので、
実際に標準出力などに表示しているわけではありません。
オブジェクトに対して ``printNl`` または ``displayNl`` メッセージを送ると、
メッセージを受け取ったオブジェクト(レシーバ)が自身の表現を標準出力に表示します。

.. code-block:: bash

    st> 'Hello, world!' printNl
    'Hello, world!'
    'Hello, world!'
    st> 'Hello, world!' displayNl
    Hello, world!
    'Hello, world!'

出力結果を見ると、``Hello, world!`` を二重に表示しています。
それぞれの最初の行の ``Hello, world!`` は ``printNl`` また ``displayNl`` メッセージによる標準出力で、
2行目は ``printNl`` と ``displayNl`` の返り値、この場合はレシーバつまり元の文字列を評価して表示しています。
2～3行で終わる計算をするときにはREPLによる対話型インタフェースは便利ですが、
プログラムの規模が大きくなると、その都度コードを打ち込むのは大変な作業です。例えば、

.. code-block:: st

    Interval extend [
        fizzbuzz [
            ^Array streamContents: [:s |
                self do: [:n |
                    | s |
                    s := '%<Fizz|>1%<Buzz|>2' % {n \\ 3 = 0. n \\ 5 = 0}.
                    stream nextPut: (s isEmpty ifTrue: [n] ifFalse: [s])
                ]
            ]
        ]
    ]

    (1 to: 10) fizzbuzz printNl

このコードを1回ぐらいならREPLから打ち込んでみてもいいかな？と感じるかも知れませんが、
この量でも何度も実行するとなると厳しいものになります。
幸運なことに、gstは起動時の引数としてファイルへのパスを指定すると、
ファイルを読み込んで実行する機能があるので、あらかじめコードをファイルに書いておき、
gstでファイル名を指定して実行できます。
テキストエディタでこのコードを ``a.st`` のような名前で保存して(Smalltalkの拡張子は.stです！)、呼び出してみると、

.. code-block:: bash

    $ gst a.st
    (1 2 'Fizz' 4 'Buzz' 'Fizz' 7 8 'Fizz' 'Buzz' )

と表示されます。


メッセージ・セレクタ
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Smalltalkではオブジェクトに対してメッセージを送るという単位でコードを実行していきます。
メッセージを受けるオブジェクトを特にレシーバとも呼びます。メッセージは、

.. code-block:: st

    receiver selector                     "C++やJavaのreceiver.selector()に相当"

の形式でレシーバに送り、実行結果として何らかのオブジェクトを必ず返します。
メッセージを送るためのセレクタすなわちレシーバオブジェクトのメソッドの呼び出し方法には、
それぞれ単項セレクタ、二項セレクタ、キーワードセレクタの3つの形式があります。
単項セレクタは一番単純な形式で引数を取りません。Cで言うところの引数voidの関数であったり、単項演算子に相当するものです。

.. code-block:: none

    -42 abs                               "42"
    5 factorial                           "120"
    #(1 2 3 4 5) size negated             "-5"

``abs`` , ``factorial`` , ``size`` そして ``negated`` が単項セレクタです。
それぞれ絶対値、階乗、長さ、符号反転のセレクタです。
``size negated`` のように、メッセージのあとにメッセージを続けるメソッドチェーンも有効です。
``#(1 2 3 4 5) size negated`` では、 ``1, 2, 3, 4, 5`` で構成される配列に対して ``size`` を送ると、要素数の5が返ってきて、
5に対して ``negated`` が送られるので-5になります。
二項セレクタは二項演算子を使うセレクタです。左辺に自身のオブジェクト、右辺に引数を1つだけ取ります。

.. code-block:: none

    2 + 3                                  "5"
    2 + 3 * 4                              "20"
    true | false                           "true"
    10 < 5                                 "false"
    'All your base' , ' are belong to us'  "'All your base are belong to us'"

gstでは二項セレクタとして扱える記号が多く、 ``%&*+,-/<=>?@\~|`` で構成される1～2文字の名前を指定できます。
後述する自作クラスまたは既存のクラスへの追加定義によって、これらの記号によるメソッドも定義できます。
``2 + 3 * 4`` の実行結果が ``20`` なのはちょっと意外に思うかも知れません。
ほとんどの言語では一般的な数学と同じように、乗算・除算記号は加算・減算記号よりも優先度が高く設定されていますが、
Smalltalkでは数学演算よりもメッセージングという概念を第一に捉えていますので、
左に書かれたものから実行します。
``2 + 3 * 4`` をより分かりやすくオブジェクト指向的に書けば、

.. code-block:: none

    2.plus(3).mul(4)

となります。計算の順序を変えたいときには、括弧を使うこともできます。

.. code-block:: st

    2 + (3 * 4)                            "14"

最後のキーワードセレクタは1つ以上の引数を取るセレクタです。引数ごとにコロンを入れた形式になります。

.. code-block:: st

    10 max: 40                             "40"
    50 between: 0 and: 100                 "true"
    Array with: 100 with: 200              "(100 200 )"

``10 max: 40`` はレシーバ ``10`` に対して引数を1つとる ``max:`` セレクタを引数 ``40`` で呼び出していて、
レシーバと引数を比較し、大きい方である ``40`` を返しています。
``50 between: 0 and: 100`` ではレシーバ ``50`` に対して ``between:`` に ``0`` 、 ``and:`` に ``100`` を引数に指定していて、
``50`` が ``0`` と ``100`` の間にあるか確認しています。
``Array with: 100 with: 200`` では ``100`` と ``200`` からなる配列を作っています。
``with:with:`` と同じキーワード名 ``with`` を使っていますがSmalltalkでは重複するキーワードでも問題なく処理されます。
それぞれのキーワードは順番を入れ替えることはできません。
``between:and:`` は ``between:`` と ``and:`` をこの順番で指定しなければいけませんし、引数を省略することもできません。
引数を省略できないのは不便ですが、多くの場合省略形のメソッドが用意されています。
例えば ``String#matchRegex:from:to:`` はレシーバの文字列が ``from:to:`` の範囲で正規表現パターンにマッチするか調べますが、
範囲が不要なとき、この場合は文字列全体を対象にするときには省略形の ``String#matchRegex:`` が用意されているので、
こちらを使った方がスッキリ書けます。

.. code-block:: none

    'obliviousWaltz' matchRegex: '[A-Za-z]+' from: 1 to: 14    "true"
    'obliviousWaltz' matchRegex: '[A-Za-z]+'                   "true"

セレクタの優先順位は単項セレクタ＞二項セレクタ＞キーワードセレクタになっています。 ``3 + 5`` の計算結果を出力したいときに、

.. code-block:: st

    3 + 5 printNl

こう書いても ``5 + printNl`` が先に評価されて、5が出力されます。 ``3 + 5`` の計算結果を正しく出力するには括弧を使用する必要があります。

.. code-block:: st

    (3 + 5) printNl


変数名・メソッド名・クラス名
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Smalltalkで使用する様々な名前…といっても変数名・メソッド名・クラス名は ``A-Z`` または ``a-z`` で始まり、
続く文字が ``A-Z`` , ``a-z`` , ``0-9`` , ``_`` でなければなりません。
Perl互換正規表現で表すと、 ``[A-Za-z][A-Za-z0-9_]*`` のパターンにマッチにする名前です。
アルファベットの大文字と小文字は厳密に区別するので、 ``abc`` と ``ABC`` は違うものとして扱われます。
名前として有効なものを以下に示します。これらはすべて ``A-Z`` または ``a-z`` で始まり、続く文字が ``A-Z`` , ``a-z`` , ``0-9`` , ``_`` です。

.. code-block:: st

    abc123 APPLE allYourBase x foo_        "OK"

``0-9`` で始まったり、 ``_`` で始まるものは名前として使用できません。

.. code-block:: none

    1024x _tmp $var                        "NG"

Smalltalkの慣例として、変数名とメソッド名は ``lowerCamelCase`` を使い、クラス名とシステム上重要なオブジェクトは ``UpperCamelCase`` を使います。

.. code-block:: st

    nil                                    "組み込みオブジェクト名"
    abs                                    "メソッド名"
    keysAndValuesDo:                       "メソッド名"
    String                                 "クラス名"
    OrderedCollection                      "クラス名"
    Transcript                             "標準出力用組み込みオブジェクト名"

このスタイルを強制するわけではありませんし、処理系によって強制もしません。
ほかの言語で実装されているライブラリをSmalltalkに移植した場合などに混乱を避けるため、
メソッド名などはそのままにしたいこともあるでしょうし、個人的な信念もあるかと思います。
CやC++、最近人気のPythonなどでは予約語が結構多く、使えたら便利な単語も使えなかったりします。
その点Smalltalkはシステム上の予約語がたったの6つしかないので割となんでもありで名前をつけられます。

.. code-block:: st

    nil true false self super thisContext

もちろんのことですが、組み込みオブジェクトやクラス名と被らないように気をつけてください。


標準出力
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

gstでオブジェクトを標準出力に書き出すにはいくつの方法があります。
一番簡単なのが、任意のオブジェクトに対して ``print`` , ``printNl`` , ``display`` , ``displayNl`` メッセージを使うものです。
``print`` と ``printNl`` はオブジェクト表現を意識した出力、
``display` と ``displayNl`` は端末出力を意識した出力をします。Nlがつくものは出力後に改行をします。
この4つはすべてのオブジェクトの頂点であるObjectで定義されているので、どのオブジェクトでも標準出力に書き出すことはできます。
が、期待した結果が得られるかはそのオブジェクト次第です。
ある程度整形して出力したいときには ``Transcript`` オブジェクトを使います。
このオブジェクトは文字列の標準出力機能を持ちます。

.. code-block:: st

    Transcript showCr: 'Hello, world!'               "Hello, world!を出力"
    Transcript space: 5                              "スペースを5つ出力"

``Transcript`` は文字列しか出力できないので、数値を出力したいときには ``asString`` メッセージを組み合わせると良いでしょう。
または ``%`` で簡単な埋め込みができるのでそれを使う方法もあります。

.. code-block:: st

    Transcript showCr: 42 asString                   "42を出力"
    Transcript showCr: '3 + 5 = %1' % #(42)          "3 + 5 = 42を出力"

明示的にstdoutオブジェクトを使う方法もありますが、一般的ではありません。

.. code-block:: st

    stdout nextPutAll: 'Hello, world!'               "Hello, world!を出力"
    stdout << 'Hello, world!'                        "Hello, world!を出力"


オブジェクトに問い合わせる
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

オブジェクトに対するメッセージング、オブジェクトとプログラマの対話によって処理を繋げていくSmalltalkでは、
リファレンスマニュアルを読むだけでなくオブジェクト自身に問い合わせるということができます。
この機能はほかのプログラミング言語にはあまり用意されていない強力な機能と言えます。
gstでは ``inspect`` または ``examine`` (調査)メッセージをオブジェクトに送ると、
どのようなオブジェクトでどのような構成か教えてくれます。
試しに ``17/25`` という分数を調査してみましょう。

.. code-block:: st

    (17 / 25) inspect   "An instance of Fraction   numerator: 17   denominator: 25"

すると、 ``17/25`` はFractionクラスのインスタンス(An instance of Fraction)であって、
分子(numerator)に17、分母(denominator)に25を持つと教えてくれます。
また、文字列 ``'abc'`` に ``inspect`` してみると、

.. code-block:: st

    'abc' inspect
    "An instance of String
    contents: [
        [1]: $a
        [2]: $b
        [3]: $c
    ]"

``'abc'`` は文字列のインスタンス(An instance of String)であって、 ``a, b, c`` という文字から構成されると教えてくれます。
素晴らしいことにこの調査の機能はユーザ定義のクラスのオブジェクトであってもうまく動作します。
銀行口座を模倣した ``Account`` クラスのコード例を示します。
まだクラスの作り方を説明していませんので、単純なものに留めておきます。

.. code-block:: st

    Object subclass: Account [
        | amount |
        Account class >> new [
            ^super new amount: 100; yourself
        ]

        amount: anInteger [
            amount := anInteger
        ]
    ]

    a := Account new.
    a inspect
    "An instance of Account
    amount: 100"

``Account`` クラスは ``Object`` クラスを継承したクラスで、内部的に口座預金を示す ``amount`` を持ちます。
このコードでは ``Account`` のインスタンスを作り、 ``amount`` を ``100`` に初期化し、そのまま ``inspect`` します。
すると、問題なく口座のインスタンスであって(An instance of Account)、預金( ``amount`` )が ``100`` であると教えてくれます。
``inspect`` のようなデバッグ出力ではなくて、オブジェクトやメソッドの概要が知りたければ ``comment`` を使うと良いでしょう。
gstでよく使われる小さな整数オブジェクトである ``SmallInteger`` に対して ``comment`` をすると、

.. code-block:: none

    SmallInteger comment
    'I am the integer class of the GNU Smalltalk system. My instances can represent signed 30 bit integers and are as efficient as possible.'

「わたしはgstシステムの整数クラスです(I am the integer class of the GNU Smalltalk system)。
SmallIntegerのインスタンスは30ビットの符号付き整数を表すことができ、
可能な限り効率的に動作します(My instances can represent signed 30 bit integers and are as efficient as possible)」。
と教えてくれます。さらに ``SmallInteger`` が解釈できるインスタンスメソッドは？と問い合わせるとその一覧をずらずらと返します。

.. code-block:: none

    SmallInteger allSelectors

    Set (#reciprocal #retryEqualityCoercing: #checkIndexableBounds: #~= #at: #odd #powerTable #printOn:paddedWith:to:base: #numerator #generality #withSignOf: #makeFixed #tan #bitAnd: #radiansToDegrees (その他いっぱい) #retryDifferenceCoercing:#storeLiteralOn: #broadcast: #instVarAt: #noMask: )

ほかにも、オブジェクトが特定のメソッドを持つかどうか( ``respondsTo:`` )、スーパークラスがなにか( ``class`` )、
子孫のクラス( ``allSubclasses`` )がなにかなども実行時に取得できます。
gstはCUI実装のため、ほかのスクリプト言語と似た側面があり、いくら問い合わせできるといっても少々不便で、
リファレンスマニュアルを読まなければならないことも多々あります(読んでみれば分かりますが、大抵ろくなことは書いてありません)。
しかし、素早くちょっと調べたいときには十分すぎる機能と言えるでしょう。


変数
--------------------------------------------------------------------------------

コード中で同じ値を使いまわしたいとき、値をあらかじめ保存しておいてあとで必要なときに計算したいとき、
コードを手順ごと簡潔に表したいときには変数を使うことになるでしょう。
変数はグローバルな領域でない限り、メソッドやブロックの先頭で宣言します。
宣言するには、変数名を空白文字区切りで列挙し、バーティカルバーで囲います。

.. code-block:: st

    | var foo hoge |

このコードでは、 ``var`` , ``foo`` , ``hoge`` の3つの変数を宣言しました。
宣言した変数には初期値として未定義値を表す ``nil`` が入っています。
変数にオブジェクトを代入するには ``:=`` を使います。ひとつの文で複数書くこともできます。

.. code-block:: st

    var := 42                             "42"
    foo := (hoge := 100) + 1              "101"

もし間違って ``=`` を書いてしまうと、Smalltalkでは同値判定のメッセージになってしまうので、気をつけてください。
もちろん、変数をあとから別のオブジェクトで書き換えることもできます。
定数は存在しないので、定数を実現するには何らかのカラクリを用意する必要があります。


ブロック
--------------------------------------------------------------------------------

多くの言語では条件付き実行をおこなう ``if`` や ``switch`` 文、繰り返しをおこなう ``while`` や ``for`` 文がありますが、
オブジェクトに対するメッセージングを単位としてコードを実行していくSmalltalkにはそのような構造がありません。
その代わりにブロックを使って同等の機能を実現しています。

ブロックはひとことで言えば無名関数のようなもので、 ``[`` と ``]`` で囲まれた文がブロックになります。
ブロックは複数行に渡って記述でき、実行するまではなにもしませんが、実行するとブロック内のコードを実行し、
最後の文の実行結果をブロック全体の実行結果として返します。

例えば ``if`` 文の代わりの ``Boolean#ifTrue:ifFalse:`` はレシーバが ``true`` か ``false`` かによって、
引数のブロックのどちらかを実行します。レシーバが ``true`` であれば ``ifTrue:`` に与えられたブロックを、
``false`` であれば ``ifFalse:`` に与えられたブロックを実行します。

.. code-block:: st

    3 < 4 ifTrue: [5] ifFalse: [6]                          "5"

この例ではまず ``3 < 4`` が評価されます。3は4より小さいので、 ``true`` になって ``ifTrue:`` 側のブロックである ``[5]`` が実行され、5が実行結果になります。
``if`` 文に相当するSmalltalkのメソッドには ``ifTrue:ifFalse:`` 、順番を逆にした ``ifFalse:ifTrue:`` 、
``true`` または ``false`` だけの ``ifTrue:`` , ``ifFalse:`` 、``nil`` かどうかで実行する ``ifNil:`` , ``ifNotNil:`` などが用意されています。

``for`` 文の代わりの ``Integer#timesRepeat:`` はレシーバの回数分ブロックを実行します。
このコードではHello, world!を10回表示して、レシーバの10を返します。

.. code-block:: st

    10 timesRepeat: [Transcript showCr: 'Hello, world!']    "10"

``Number#to:do:`` は ``timesRepeat:`` と違い、開始と終了の値を指定できます。
また、ブロックの引数として今の値を渡します。

.. code-block:: st

    1 to: 10 do: [:x | x printNl]                           "1"

単独でブロックを使うことはあまりありませんが、
``value`` , ``value:`` , ``valueWithArguments:`` メッセージによってブロックを実行できます。

.. code-block:: none

    [2 + 3] value                                           "5"
    [:x | x * 2] value: 100                                 "200"
    [[42] value] value                                      "42"
    [:x :y | x + y] value: -5 value: 9                      "4"
    [:x :y | x + y] valueWithArguments: #(-5 9)             "4"


データ型
--------------------------------------------------------------------------------

C, C++やJavaのような言語では、コンパイル時や実行前に型を明らかにして、
その計算が妥当であるかどうか調べた結果、計算できないコードがあれば、
実際に実行されないとしてもエラーにして1行も実行させてくれません。
このような静的型付けを採用している言語では、実行時に初めて計算できるかどうかを判断する動的型付けに比べて、
型の堅牢さをある程度約束します。

一方で、常に型のことを考えたコードを書く必要があり、
終わりよければすべてよしな書き捨てやプロトタイプ開発用コードを書きたい場合にはちょっと大変です。
もしあなたが普段からPythonやRubyに慣れ親しんでいて、いきなりC++で設計してくれなんて言われた日には、特にそう思うかも知れません。
Smalltalkは実行時に初めて計算できるか判断する強い動的型付けを採用していて、
PythonやRubyのデータ型と同じように扱うことができます。

.. code-block:: st

    a + abs(b)         "C, C++, Javaなど。a + abs(b)が計算できるか実行前に検査する"
    a + b abs          "Smalltalk。実行時に動けばよい"

Smalltalkで主要なデータ型と言ってもすべてがオブジェクトなので、クラスと言った方が正しいでしょう。
主要なクラスには次のようなものが用意されています。

* 未定義値(UndefinedObject)
* 真偽値(True, False)
* 実数・整数・分数(Number, Integer, Fraction)
* 文字(Character)
* 文字列・シンボル(String, Symbol)
* Key-Valueペア(Association)
* 配列・動的配列・辞書(Array, OrderedCollection, Dictionary)
* ストリーム(ReadStream, WriteStream)

gstのクラスはほかのSmalltalk処理系と比較して少ない方ですが、
それでも列挙するには数ページを要しますのですべてを確認したいのであれば、
`https://www.gnu.org/software/smalltalk/manual-base/html_node/Base-classes.html <https://www.gnu.org/software/smalltalk/manual-base/html_node/Base-classes.html>`_
を見ると良いでしょう。このページでは単純なメソッドのリファレンスだけでなくクラスの上下関係も確認できるので、
gstのオブジェクト体系を知ることができます。
このクラスリファレンスはgstに付属してくる ``gst-doc`` でも出力できます。

.. code-block:: st

    s := (nil allSubclasses asArray collect: [:x | x printString]) join: ' '.
    Smalltalk system: 'gst-doc -F HTML ' , s


未定義値
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
未定義値は ``nil`` です。 ``nil`` は ``UndefinedObject`` クラスのインスタンスで、
変数や配列の初期値が特に指定されないときの初期値に使用されます。
``nil`` は特別扱いされるものではなく、Smalltalkを構成するオブジェクトのひとつなので、メッセージを受け付けます。
``UndefinedObject`` はすべてのオブジェクトの祖である`Object`のサブクラスであることから、
``nil`` は ``Object`` で定義されているメソッドを呼び出すことができます。
特別扱いされることのないオブジェクトであることを証明するために、
オブジェクトの文字列表現を得る ``displayString`` とクラスオブジェクトを返す ``class`` を使ってみましょう。
するとそれぞれ ``'nil'`` という文字列と、 ``UndefinedObject`` を得られます。

.. code-block:: st

    nil displayString                       "'nil'"
    nil class                               "UndefinedObject"

``nil`` は未定義値であって、真偽値ではありませんし、真でも偽でもありません。
もし、オブジェクトが未定義値かどうか調べたいときには、次のメッセージが役に立ちます。

.. code-block:: st

    nil isNil                               "true"
    nil notNil                              "false"
    nil ifNil: [100] ifNotNil: [200]        "100"


真偽値
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

真偽値は ``True`` と ``False`` のインスタンスである ``true`` と ``false`` です。
値の比較をしたときや ``is-`` から始まるメッセージでオブジェクトのテストをしたときに真偽値を得ることができます。

.. code-block:: none

    100 = 200                               "false"
    100 < 200                               "true"
    100 ~= 200                              "true"
    100 >= 200                              "false"
    100 odd                                 "false"
    false not                               "true"
    #(1 2 3 4 5) isArray                    "true"
    'Lorem Ipsum' startsWith: 'Lorem'       "true"

``true`` , ``false`` は主に制御構文や論理演算に使います。
制御構文は ``ifTrue:ifFalse:`` をはじめとした条件分岐のメソッドを使うことを前に書きました。
もう一方の論理演算には正格評価と短絡評価があります。

正格評価は左辺の値に関わらず文全体が評価されるのに対して、短絡評価は左辺で論理の条件を満たせば右辺は評価しません。
そのため、右辺にはブロックを指定します。

.. code-block:: st

    "正格評価"
    true | false                            "true"
    true & false                            "false"
    true xor: false                         "true"

    "短絡評価"
    true or: [42]                           "true"
    false or: [42]                          "42"
    true and: [42]                          "42"
    false and: [42]                         "false"
    (true and: [false]) or: [42]            "42"

真偽値は数値と暗黙的に可換ではありません。
悪しきCの尖兵たちは真偽値を数値と同じ……というよりもすべてを数値と同等に考えていますが、これらは明確に区別されます。
Cと同じ考え方で勝手に ``true`` を ``1``に (正確には0以外の値)、 ``false`` を ``0`` に対応づけるのはナンセンスです。
実際 ``true`` を ``-1`` に対応づける言語もあります。 ``true`` は ``true`` であって、 ``false`` は ``false`` だということです。

しかし真偽値を特定の数値に変換したい需要はないとは言い切れません。
Smalltalkで真偽値から数値に変換する方法のひとつは移植性のある次の書き方です。
ただし、これはとても冗長に見えるのが難点です。

.. code-block:: st

    aBoolean ifTrue: [1] ifFalse: [0]

まったく幸運なことに、gstではCユーザのために ``asCBooleanValue`` を用意しています。
「Cの真偽値の値を得る」という明確でイケてるメソッド名で、 ``true`` なら ``1`` 、 ``false`` なら ``0`` を得られます。

.. code-block:: st

    aBoolean asCBooleanValue


整数・浮動小数点数・分数
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

整数は数値計算の主となるオブジェクトで ``Integer`` を継承した
``SmallInteger`` , ``LargePositiveInteger`` , ``LargeNegativeInteger`` のインスタンスです。
``Integer`` は数値全体を表す ``Number`` クラスのサブクラスでもあり、数値の一部分として整数が存在する扱いになっています。

実行環境のワード長で簡単に扱える範囲の整数は ``SmallInteger`` で、
それを超えると多倍長整数である ``LargePositiveInteger`` と ``LargeNegativeInteger`` に自動的に変換されます。
したがって、コードを書く際には扱う値の範囲を意識する必要はありません。
整数を扱うには、単純に整数を書きます。先頭に ``-`` を付加すれば負数になります。

.. code-block:: st

    0                                       "0"
    42                                      "42"
    100                                     "100"
    -56                                     "-56"
    100000000000000000000000000000000       "100000000000000000000000000000000"

基数を指定した整数は ``16rFF`` のように、基数rプリフィクスを付けます。
2～36の任意の基数を指定でき、11進数からは ``A-Z`` のアルファベットが使用されますが、これらはすべて大文字でなければいけません。

.. code-block:: st

    16rFF                                   "255"
    2r10001011                              "139"
    -8r640                                  "-416"

数値がすることと言えば、なんといっても数値計算です。
数値用にはたくさんのメソッドが用意されていて全部を紹介することはできませんが、ほんの一部を次に示します。

.. code-block:: st

    -7 + 5                                   "2"
    -7 - 5                                   "-12"
    -7 * 5                                   "-35"
    -7 / 5                                   "-7/5"
    -7 // 5                                  "-2"
    -7 \\ 5                                  "3"
    -7 = 5                                   "false"
    -7 ~= 5                                  "true"
    -7 < 5                                   "true"
    -7 >= 5                                  "false"
    -7 quo: 5                                "-1"
    -7 rem: 5                                "-2"
    -7 min: 5                                "-7"
    -7 max: 5                                "5"
    -7 abs                                   "7"
    -7 negated                               "7"
    -7 raisedTo: 5                           "-16807"
    -7 negative                              "true"
    -7 positive                              "false"
    -7 sign                                  "-1"
    -7 isInteger                             "true"
    5 bitShift: 2                            "20"
    5 factorial                              "120"
    256 lcm: 96                              "32"

``//`` と ``\\`` は負の無限大への丸めをし、 ``/`` は分数を表す ``Fraction`` クラスのインスタンスを返します。
小数点以下の値を使いたいときには浮動小数点数の出番です。

浮動小数点数にはCの ``float`` 相当である ``FloatE`` 、 ``double``相当の`FloatD` 、 ``long double`` 相当の ``FloatQ`` があります。
単純に浮動小数点数リテラルを書いたときには ``FloatD`` になりますが、
サフィックス ``e`` , ``d`` , ``q`` で、どの精度にするか指定できます。

.. code-block:: st

    3.14 class                              "FloatD"
    3.14e class                             "FloatE"
    3.14d class                             "FloatD"
    3.14q class                             "FloatQ"
    3.14q0                                  "3.14"
    3.14q1                                  "31.4"
    3.14q2                                  "314.0"
    3.14 asInteger                          "3"
    3.14 isNumber                           "true"
    3.14 isInteger                          "false"

浮動小数点数も整数同様の数値計算ができます。違いは結果の多くが浮動小数点数で返ってくるということです。
数値の精度を落としなくなければ分数や固定小数点数で計算することもひとつの手です。
``/`` で分数を、 ``s`` サフィックスで固定小数点数を作れます。

.. code-block:: none

    21/77                                   "3/11"
    (3/11) numerator                        "3"
    (3/11) denominator                      "11"
    (3/11) + (8/11)                         "1"
    (3/11) + (5/8)                          "79/88"
    (3/11) + 2                              "25/11"
    21 + 10s4                               "31.0000s4"
    10.5s4 + 0.025s6                        "10.525000s6"

数値オブジェクトは整数と分数のように混ぜて計算することができます。
計算結果は ``Integer`` ＜ ``ScaledDecimal`` ＜ ``Fraction`` ＜ ``Float`` と浮動小数点数の方向へ拡張されていきます。

.. code-block:: none

    42 + 3.14                               "45.14"
    42 + (22/7)                             "316/7"
    3.14 + (22/7)                           "6.282857142857143"
    42 + 3.14s10                            "45.1400000000s10"
    3.14s10 + 12.25                         "14.39"
    (22/7) + 3.14s10                        "2199/350"


文字
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1バイト文字( ``Character`` )またはUnicode文字( ``UnicodeCharacter`` )のインスタンスです。
主に文字列やUnicode文字列の構成要素として利用します。
リテラル表現は ``$`` 記号のあとに任意の1文字を続けるか(空白文字も含む)、
文字コードを直接指定します。動的に文字を作るときには、``Character`` のクラスメソッドを呼び出します。

.. code-block:: none

    $a                                      "$a"
    $<97>                                   "$a"
    $<16r61>                                "$a"
    $$                                      "$$"
    (Character value: 130) class            "Character"
    (Character codePoint: 130) class        "UnicodeCharacter"

変換・テスト用のメソッドには次のようなものがあります。

.. code-block:: none

    $a asInteger                            "97"
    $a asUppercase                          "$A"
    $a < $b                                 "true"
    $a isAlphaNumeric                       "true"
    $a isDigit                              "false"
    $a isDigit: 16                          "true"
    $a isLetter                             "true"
    $a isLowercase                          "true"
    $a isUppercase                          "false"
    $a isSeparator                          "false"
    $a isCharacter                          "true"
    $a * 10                                 "'aaaaaaaaaa'"


Key-Valueペア
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``->`` は関係、Key-Valueペアを作るメッセージです。
``10->20`` と書けば、キーが ``10`` 、値が ``20`` の関係を作ることができます。
キーと値にするオブジェクトに制限はなく、どんなオブジェクトでも指定できます。
Key-Valueペアの役割は小さく、 ``key:`` でキーを、 ``value:`` で値をセットして、keyとvalueで取り出すだけです。

.. code-block:: none

    10->'ALL YOUR BASE'                     "10->'ALL YOUR BASE'"
    Association key: nil value: $a          "nil->$a"
    10->20->30 key                          "10->20"
    (true->#loremIpsum) value               "#loremIpsum"

    | a |
    a := 'Lorem Ipsum'->#(1 2 3)            "'Lorem Ipsum'->(1 2 3)"
    a key: a key asUppercase                "'LOREM IPSUM'->(1 2 3)"

ちょっとしたオブジェクトですが、
Key-Valueのペアを作ることで特定の手続きが綺麗に書けたりして小回りの効くオブジェクトでもあります。
また、後述の辞書の構成要素にもなっています。


配列・動的配列
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

データ構造の基本と言えば配列です。
キューやスタック、ハッシュテーブルといった応用的なデータ構造も内部的には配列を使って実装されています。
gstの配列は ``Array`` クラスのインスタンスで固定長です。
前方、後方または中間への要素の挿入や削除によって長さを変えることはできませんが、配列の作成時に長さを動的に指定できます。
配列を作るには、配列リテラルを使用できます。オブジェクトリテラルを空白文字区切りで並べて、
``#(`` と ``)`` で囲うと、その部分が配列になります。

.. code-block:: none

    #()                                     "()"
    #(1 2 3 4 5)                            "(1 2 3 4 5 )"

このリテラル表現はメッセージングを含むことができません。
文の計算結果を要素として配列を作るときには、文の終わりに ``.`` を付け、 ``#(`` と ``)`` の代わりに、 ``{`` と ``}`` で囲います。

.. code-block:: st

    {1. 2 + 3. 4 * 5}                       "(1 5 20 )"

``Array#new:`` や ``Array#new:withAll:`` を使うと好きな要素数の配列を得られます。
要素が明示的に指定されない限りすべて ``nil`` になります。

.. code-block:: st

    Array new: 5                            "(nil nil nil nil nil )"
    Array new: 5 withAll: 42                "(42 42 42 42 42 )"

配列からN番目の要素を取り出すいちばんうまい方法は ``at:`` を使うことです。
Smalltalkの添字は1から始まるので、最初の要素は1、最後の要素はNになります。
最初の要素や最後の要素を要素数に関係なく取り出したければ、
``at:`` の代わりに ``first`` と ``last`` を使うのもより抽象的でわかりやすい方法です。

.. code-block:: st

    a := #(10 20 30 40 50)                  "(10 20 30 40 50 )"
    a at: 3                                 "30"
    a at: 1                                 "10"
    a first                                 "10"
    a first: 3                              "(10 20 30 )"
    a at: a size                            "50"
    a last                                  "50"
    a last: 3                               "(30 40 50 )"

配列にはなんでも格納できますし、入れ子にしても構いません。
配列要素のオブジェクトが属するクラスを統一する必要はありませんし、クラスオブジェクトを要素とすることもできます。

.. code-block:: none

    #(true false nil)                       "(true false nil )"
    #(1 2 (3 4) 5)                          "(1 2 (3 4 ) 5 )"
    ({1. 2. 3. 'abc'. Array} at: 5) new: 3  "(nil nil nil )"
    a := Array new: 5                       "(nil nil nil nil nil )"
    a at: 3 put: 42                         "42"
    a                                       "(1 2 42 4 5 )"

要素数を動的に増やしたり減らしたりできる配列が欲しい場合には、 ``OrderedCollection`` を使います。
要素へのアクセスは ``Array`` と同じですが、追加と削除用のメソッドが追加されています。
``OrderedCollection`` のインスタンスを作るには、
クラスメソッドの ``new`` を呼び出すまたは ``asOrderedCollection`` で配列から変換してくるのが簡単です。

.. code-block:: st

    a := OrderedCollection new.             "OrderedCollection ()"
    b := #(10 20 30) asOrderedCollection    "OrderedCollection (10 20 30 )"
    a add: 5                                "5"
    a addFirst: 2                           "2"
    a add: 0 afterIndex: 1                  "0"
    a                                       "OrderedCollection (2 0 5 )"
    b removeFirst                           "10"
    b removeLast                            "30"
    b removeAt: 1                           "10"
    b                                       "OrderedCollection ()"

配列に複数の要素を格納していく場面もしばしばあります。例えば、こんな感じで……

.. code-block:: st

    a := OrderedCollection new              "OrderedCollection ()"
    a add: 10                               "10"
    a add: 20                               "20"
    a add: 30                               "30"
    a                                       "OrderedCollection (10 20 30 )"

要素の追加のたびにレシーバを書くのはちょっと冗長にも見えます。
今は変数名が ``a`` で短いので我慢もできるかもしれませんが、
``whatALongVariableName`` のように長い変数名だったときはうんざりしてきますね。
``add:`` の返り値が ``self`` であれば、こんな書き方もできますが、

.. code-block:: st

    ((a add: 10) add: 20) add: 30     "Object: 10 error: did not understand #add:"

``add:`` の返り値は追加したオブジェクトなのでうまくいきません。
そこでこういう状況にも対応できるようなカスケードと呼ばれる機構がSmalltalkには備わっています。
カスケードを使うと、1つの文で同一のレシーバに対して連続でメッセージを送ることができます。
このときの文全体の返り値は最後のメッセージングの返り値になります。

.. code-block:: st

    a := OrderedCollection new              "OrderedCollection ()"
    a add: 10; add: 20; add: 30             "30"
    a                                       "OrderedCollection (10 20 30 )"

また、レシーバ自身を返す ``yourself`` メソッドというのもあります。
これを単に使っただけではレシーバ自身が返って来るので意味はありません。

.. code-block:: none

    100 yourself                            "100"
    'All your base' yourself                "'All your base'"

が、カスケードと組み合わせるとその威力を発揮します。
次のようなコードを書けばインスタンスの取得と値の挿入を一度に済ませることができます。

.. code-block:: st

    a := OrderedCollection new add: 10; add: 20; add: 30; yourself
    "OrderedCollection (10 20 30 )"

このイディオムは後述のクラスのインスタンス作成でよく使われるものです。
高機能な反面、やたらに使うとコードの明確さが失われるので使い所が難しい機能でもあります。

配列への操作をいくつか紹介します。
配列はIterableクラスを継承しているので、シーケンシャルアクセスが得意です。
Lispの影響を受けているSmalltalkは畳み込み計算用のメソッドが多数用意されています。

.. code-block:: st

    a := #(5 2 4 8 9 1 3 7 6 0)             "(5 2 4 8 9 1 3 7 6 0 )"
    a size                                  "10"
    a , #(10 20 30)                         "(5 2 4 8 9 1 3 7 6 0 10 20 30 )"
    a reverse                               "(0 6 7 3 1 9 8 4 2 5 )"
    a sorted: [:x :y | x > y]               "(9 8 7 6 5 4 3 2 1 0 )"
    a do: [:x | x printNl]                  "5 2 4 8 9 ... を出力"
    a keysAndValuesDo: [:x :i | ('%1: %2' % {i. x}) displayNl]
                                            "1: 5, 2: 2, 3: 4, ... を出力"
    a anySatisfy: [:x | x = 0]              "true"
    a includes: 9                           "true"
    a count: [:x | x > 5]                   "4"
    a select: [:x | x > 5]                  "(8 9 7 6 )"
    a collect: [:x | x * 2]                 "(10 4 8 16 18 2 6 14 12 0 )"
    a inject: 0 into: [:x :y | x + y]       "45"
    a fold: [:x :y | x + y]                 "45"
    a copyFrom: 1 to: 3                     "(5 2 4 )"


文字列・シンボル・バイト列
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

文字列( ``String`` , ``UnicodeString`` )は配列の仲間で、文字で構成される配列という扱いになっています。
クラスの関係を見てみると、

.. code-block::

    Object
    Iterable
    Collection
        SequenceableCollection
        ArrayedCollection
            Array
            ByteArray
            CharacterArray
            String
                Symbol
            UnicodeString

こうなっていて、文字列にも配列で使えたメソッドが利用可能なことを匂わせていて、実際それは正しい認識です。

.. code-block:: none

    'Hello, world!' size                    "13"
    'all your base' at: 5                   "$y"
    'all your base' , ' are belong to us'   "'all your base are belong to us'"
    'all your base' copyFrom: 10            "'base'"

シンボル( ``Symbol`` )は文字列のサブクラスで動作はほぼ同じですが、
同じ内容であれば同じオブジェクトを指します。オブジェクトの同値比較は ``=`` ですが、オブジェクトの比較は ``==`` を使います。

.. code-block:: st

    a := 'LoremIpsum'
    b := 'LoremIpsum'
    c := #LoremIpsum
    d := #LoremIpsum

    a = b                                   "true"
    a == b                                  "false"
    c = d                                   "true"
    c == d                                  "true"

バイト列( ``ByteArray`` )は構成要素が文字の代わりに0～255の数値になったものです。
バイトデータのやりとりに便利で、 ``at:`` の亜種である ``intAt:`` や ``ushortAt:`` などが追加定義されています。

.. code-block:: none

    #[97 98 99]                             "ByteArray (97 98 99 )"
    #[97 98 99] ushortAt: 1                 "25185"
    #[97 98 99] asString                    "'abc'"


辞書
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

辞書はハッシュで実装されたKey-Valueペア( ``Association`` )の集合です。
ほかの言語では単にハッシュと呼ばれたり、マップ、連想配列などと呼ばれるものです。
配列が連続的な要素の扱いが得意なのに対して、辞書は疎で不連続な空間の扱いに長けています。

辞書を作るには ``Dictionary`` のクラスメソッドを呼び出すか、配列から変換します。
残念ながら辞書のリテラル表現はありません。
配列から変換するときには、Key-Valueペアの配列を指定しなければなりません。

.. code-block:: st

    Dictionary new                          "Dictionary ()"
    Dictionary from: {
        42->'all your base'.
        #(1 2 3 4 5)->'Hello, world!'.
        'hoge'->[Transcript show: 'fuga']
    }
    "Dictionary (
        42->'everything'
        (1 2 3 4 5 )->'Hello, world!'
        'hoge'->a BlockClosure
    )"

辞書からオブジェクトを取り出すには配列や文字列同様にat:が使えます。
``at:`` で指定したキーがなければエラーになりますが、 ``at:ifAbsent:`` を使うとキーがなかったときの処理を指定できます。

.. code-block:: st

    d := Dictionary from: {
        42->'all your base'.
        #(1 2 3 4 5)->'Hello, world!'.
        'hoge'->[Transcript show: 'fuga']
    }

    d at: 42                                "'everything'"
    d at: 100                               "SystemExceptions.NotFound例外"
    d at: 100 ifAbsent: [100]               "100"

オブジェクトをセットするには ``at:put:`` です。
``at:put:`` はキーがなくても失敗しませんし、すでにキーがあれば単純に上書きされます。
削除にはKey-Valueペアを削除する ``remove:`` , キーに該当するペアを削除する ``removeKey:`` , ``removeAllKeys:`` があります。

.. code-block:: st

    d := Dictionary from: {
        'hoge'->50.
        'fuga'->30.
        'foobar'->100
    }
    d at: 500 put: 'test'                   "'test'"
    d
    "Dictionary (
        'foobar'->100
        'fuga'->30
        'hoge'->50
        500->'test'
    )"

    d removeKey: 'fuga'                     "'30'"
    d removeKey: 'fuga' ifAbsent: ['N/A']   "'N/A'"

辞書の値すべてを列挙したいときには、 ``keysAndValuesDo:`` を使います。
ここでの値の取り出し順はバラバラなので、特定の順番、例えばキーでソートされた順番で取り出したい場合には、
``keys`` でキー一覧を取得してソートしてから値にアクセスする方法をとります。

.. code-block:: st

    d := Dictionary from: {
        'aaa'->97.
        'bbb'->98.
        'ccc'->99
    }
    d keysAndValuesDo: [:k :v |
        Transcript showCr: '%1 %2' % {k. v}
    ]
    "aaa 97"
    "ccc 99"
    "bbb 98"

    d keys sorted do: [:k |
        Transcript showCr: '%1 %2' % {k. d at: k}
    ]
    "aaa 97"
    "bbb 98"
    "ccc 99"

ストリーム
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

なんらかのデータを書いたり読んだり、エンコードしたりデコードしたり、
圧縮したり解凍したりというときには、よくこのストリームを使います。
Smalltalkではとにかくストリームを使います。
ストリームには読み込みストリーム( ``ReadStream`` )、書き込みストリーム( ``WriteStream`` )、
読み書きストリーム( ``ReadWriteStream`` )の3つがあり、ファイル操作と同じ感覚でオブジェクトの操作ができるようになります。

読み込みストリームを作るには、 ``ReadStream#on:`` に配列を渡すか、配列オブジェクトの ``readStream`` を呼び出します。

.. code-block:: st

    text := 'all your base'                 "'all your base'"
    s := text readStream                    "a ReadStream"
    s next                                  "$a"
    s next: 8                               "'ll your '"
    s position: 0                           "a ReadStream"
    s next: 3                               "'all'"

ストリームを構成する要素は文字に限らず、数値のストリームも作ることができます。次に示すのは書き込みストリームの例です。

.. code-block:: st

    s := WriteStream on: Array new          "a WriteStream"
    s nextPut: 100                          "100"
    s nextPut: 200                          "200"
    s nextPut: 300                          "300"
    s contents                              "(100 200 300 )"

書き込んだオブジェクトは ``contents`` によって配列として取り出すことができます。
このときファイルの読み書き位置は変更しません。
また、 ``streamContents:`` を使えばストリームで配列を初期化もできたりします。

.. code-block:: st

    a := Array streamContents: [:stream |
        stream nextPut: 100.
        stream nextPut: 200.
        stream nextPut: 300
    ]
    "(100 200 300 )"


コード・パッケージの読み込み
--------------------------------------------------------------------------------

コードの規模が大きくなると、ある単位で区切ってファイルに分割することがよくあります。
その単位はモジュールであったりクラスであったり名前空間であったり様々ですが、
ある程度のまとまりのコードを用意しておいて、必要になったときに読み込むようにすれば移植性・保守性を高く保てるでしょう。

``mycode.st`` というファイルにコードが書かれていて、別のファイルから ``mycode.st`` に書かれたコードを読み込みたいときには、

.. code-block:: st

    FileStream fileIn: 'mycode.st'

と書きます。このコードが実行されると ``mycode.st`` の中身も実行されるので、
クラス定義を書いておけば呼び出し元でそのクラスを利用することができるようになります。

コードを読み込むほかにパッケージを読み込むこともあります。パッケージとは複数のコードをまとめたものです。
gstにはいくつかのパッケージ、例えば複素数計算、データベース接続、ソケット、I18N、XML、ZLibなどは最初からgstに付属してきます。
これらは自動的に読み込まれるものではなく明示的に読み込まなければなりません。

.. code-block:: st

    PackageLoader fileInPackage: 'I18N'

パッケージを読み込むと新しいクラスが追加されたり、既存のクラスに追加定義されることがあります。
思わぬ変更も加わるので競合しないように気をつける必要もあるでしょう。
パッケージは自作もできます。簡単なパッケージ定義用XMLとソースコードを用意して、
``gst-package`` コマンドでパッケージ化します。
具体的なパッケージ化の方法は公式ドキュメントに記載されているので、そちらを参照してください。


クラス
--------------------------------------------------------------------------------

今まではgstシステムに初めから用意されているオブジェクトを操作する方法を紹介してきました。
数多くのオブジェクトはプログラミングする上でとても便利な機能を提供してくれますが、
実際になんらかの目的を持ってコードを書くときには、
既存のオブジェクトを組み合わせて新たなオブジェクトを作る必要があり、
その問題解決のために用意されているのが新たなクラスの定義です。

新たなクラスを作るにはなんらかの既存のクラスを継承する必要があります。
継承すると継承元のクラスの性質をすべて引き継ぐので、
例えば文字列クラスを継承すれば文字列と同じような挙動になりますし、
配列を継承すれば配列と同じような挙動になります。
また、継承して継承元のメソッドの定義を変えたり新しくメソッドを定義すれば、
継承元の新たな派生としてのクラスを定義できます。


クラスを俯瞰してみる
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

gstのクラスはほかの処理系ほどではありませんが、たくさんのクラスがあります。

`https://www.gnu.org/software/smalltalk/manual-base/html_node/Base-classes.html <https://www.gnu.org/software/smalltalk/manual-base/html_node/Base-classes.html>`_

ここを見るといくつか面白いことが見て取れます。

``Iterable`` は反復可能クラスで、集合に対する反復処理を定義したクラスです。
ただし、集合としての機能は持っておらず、抽象クラスとしての役割を果たしています。

そのサブクラスである ``Collection`` は集合クラスで、複数の値をひとまとまりとして扱うクラスになっていて、
``Dictionary`` や ``Set`` 、 ``Array`` などが含まれます。 ``Collection`` は ``Iterable`` を継承しているので、
もちろん反復可能ですし、さらに ``Iterable`` が持っていなかった集合という性質も持っていますが、
``Collection`` は抽象クラスとしてサブクラスに集合としての機能を託しています。

``Collection`` のサブクラスである ``SequenceableCollection`` は順序配列を扱うクラスで、
``Iterable`` の反復可能と ``Collection`` の集合という性質を受け継ぎ、さらに順序集合という性質も持っています。
このサブクラスとしては ``Array`` , ``OrderedCollection`` , ``ByteArray`` , ``String`` などがあり、
``Array`` と ``OrderedCollection`` は任意のオブジェクトを格納する目的で、
``ByteArray`` は0～255の値を持つバイトシーケンスを扱う目的で、 ``String`` は文字の配列を扱う目的というように、
用途ごとのサブクラスを用意しています。

また、 ``Integer`` や ``Character`` がどのようなクラスのサブクラスになっているか見てみると、
``Magnitude`` というクラスが大元になっています。
``Magnitude`` は大小比較可能クラスで、これも ``Collection`` 同様に抽象クラスです。
大小比較可能というのは、 ``<`` や ``>=`` で大小比較ができることを意味しています。
大小比較できることから、 ``min:`` と ``max:`` というメソッドもここで定義されています。

例外を取り扱う ``Exception`` クラスを見れば、例外もその中で細分化されていて、
代表的な ``SystemException.InvalidValue`` のサブクラスとして、
空配列に対する ``EmptyCollection`` や不正な引数に対する ``InvalidArgument`` などの例外が用意されています。

gstは大きなシステムではないのでクラス関係を把握するのは容易ですし、
Smalltalkに限らず自作のクラスを設計するときにこのクラス関係は大いにお手本になることと思います。


クラスの基本
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

クラスを作るには既存のクラスを継承する必要があると言いました。
一番簡単なクラスはあるクラスを継承しただけのクラスになるでしょう。
自作の ``MyObject`` クラスを定義するために ``Object`` を継承してみます(大抵の場合、 ``Object`` を継承することになります)。
すると……

.. code-block:: st

    Object subclass: MyObject [
    ]

これだけのコードで ``MyObject`` クラスは ``Object`` クラスの機能を受け継ぎました。
しかし ``MyObject`` では新たなインスタンス変数やメソッドを定義していないので、
``Object`` と比べて名前が違うだけで同じ挙動を示します。

``MyObject`` のスーパークラスである ``Object`` は ``new`` メッセージを受け取るとそのインスタンスを作って返します。
``MyObject`` は ``Object`` を継承しているので、同じように ``new`` でインスタンスを作ることができます。

.. code-block:: st

    MyObject new                            "An instance of MyObject"

これだけでは面白くないので、値の設定機能をつけてみましょう。
クラスの中で変数宣言をするとそれはインスタンス変数になり、
インスタンスごと独立して管理されます。

インスタンス変数として ``x`` を定義し、 ``x`` を書き換えるための ``value:`` 及び ``x`` にアクセスするための ``value`` を定義します。
普通のメソッド定義をするとインスタンスメソッドとしての定義になります。
クラス外からインスタンス変数は直接アクセスできないので、それ用のメソッドを作る必要があります。

.. code-block:: st

    Object subclass: MyObject [
        | x |
        value: anObject [
            x := anObject
        ]

        value [
            ^x
        ]
    ]

メソッド内で返り値を指定するときには、返り値とする文の先頭に ``^`` を付けます。
返り値を指定しないときは ``self`` つまり自分自身がデフォルトの返り値になります。

ここで、例えば ``x`` の値をインスタンス作成と同時に ``100`` に設定したいということがあったりします。
これをするには、 ``new`` メソッドの上書きをしてその中で値を設定しなければなりません。
``new`` はクラスメソッドなので、 ``>>`` を使ったちょっと特殊な宣言をします。
次のコード中の ``MyObject class >>`` がそれです。

.. code-block:: st

    Object subclass: MyObject [
        | x |
        MyObject class >> new [
            | r |
            r := super new.
            r value: 100.
            ^r
        ]

        value: anObject [
            x := anObject
        ]

        value [
            ^x
        ]
    ]

``self`` と ``super`` は特殊な意味を持ちます。
``self`` も ``super`` も自分自身を指しますが、メソッドを現在のクラスから検索するか、
親のクラスから検索するかの違いがあります。
``super new`` をすると、処理系はスーパークラスをはじめとして、 ``new`` メソッドをクラスを遡って探します。
すると ``MyObject`` のスーパークラスである ``Object`` が ``new`` に対してインスタンスを返すので、
変数 ``r`` には ``super new`` で作られたインスタンスが代入されます。
新しいインスタンスの ``x`` の中身は ``nil`` ですが、 ``r value: 100`` によって ``x`` に ``100`` が代入されます。

メソッドはすべて仮想関数として扱われ、スーパークラスで定義されているメソッドはサブクラスでも有効で、
サブクラスで書き換えることもできます。

Smalltalkにはインスタンスメソッドとクラスメソッドという区別はあるものの、
明確なコンストラクタというものは存在しません。
クラスメソッドの中でインスタンスを作成する ``new`` やそれに準ずるメソッドを呼べばそれがコンストラクタの役割を果たします。
また、クラスメソッド同士で初期化のたらい回しも可能です。

インスタンスの作成と初期値の代入にはカスケードを使ったお決まりのイディオムがあり、今のインスタンス作成を書き直すと、

.. code-block:: st

    MyObject class >> new [
        ^super new value: 100; yourself
    ]

とも書けます。

``MyObject`` の値を初期化とともに指定したいときには、新たなクラスメソッド ``new:`` を定義して、

.. code-block:: st

    MyObject class >> new: anInteger [
        ^super new value: anInteger; yourself
    ]

となるでしょう(勝手に ``100`` に設定するよりイケてる設計です)。

``MyObject`` のインスタンスが複数あるとき、
``MyObject`` のインスタンスが持つ ``x`` の値を足し合わせた値を持つ
新たな ``MyObject`` のインスタンスを得たいということもあるかも知れません。
より直感的な操作のため ``+`` メソッドを追加することとします。

.. code-block:: none

    + rhs [
        ^self class new: self value + rhs value
    ]

``self class`` はインスタンスのクラスつまりここでは ``MyObject`` を指します。
もし ``+`` メソッドが ``MyObject`` のサブクラスで実行されれば、
サブクラスを指すようになります。 ``self class`` の代わりに ``MyObject new:`` としてしまうと、
サブクラスでも ``MyObject#new:`` が呼び出されてしまうので、使い勝手がよくありませんし、思わぬ不都合を招きます。


追加定義
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

追加定義は既存のシステムに手を加えるために使い所を誤ると良くない効果を及ぼしますが、
その反面強力な機能でもあるため、Smalltalkでは好んで使われます。

Smalltalkのメソッドはすべて仮想関数なので ``Integer`` クラスに追加定義すると、
``Integer`` を継承している ``LargeInteger`` や ``SmallInteger`` にも見た目上、同じメソッドが追加されます。
見た目上と言ったのは、実際に追加定義がされるのは追加定義されたクラスだけであって、
サブクラスではスーパークラスを遡って実行するためです。

もちろん、頂点のオブジェクトである ``Object`` に追加定義をすればすべてのオブジェクトに対して影響が現れます。
では、 ``Object`` に対してひとつ追加定義をしてみましょう。なにがなんでも ``42`` を返す ``fortyTwo`` メソッドを追加してみます。

.. code-block:: st

    Object extend [
        fortyTwo [^42]
    ]

``extend`` が追加定義用のキーワードです。
これで ``Object`` とそのサブクラスのインスタンスが ``42`` を返すメソッド ``fortyTwo`` が定義できました。
``42`` を返してなにが楽しいのかは分かりませんが、とにかくできるようにはなったのです。
``Object`` のインスタンスであっても、 ``SmallInteger`` のインスタンスであっても、 ``Array`` のインスタンスであっても42を返します。

.. code-block:: none

    Object new fortyTwo                    "42"
    42 fortyTwo                            "42"
    #(1 2 3 4 5) fortyTwo                  "42"

ここで、配列クラス以下では ``42`` の代わりに ``100`` を返したいという要求が出てきたとします。
この場合は、例えば ``Collection`` に対して追加定義をすれば、思った通りの動作をするでしょう。

.. code-block:: none

    Collection extend [
        fortyTwo [^100]
    ]

    Object new fortyTwo                    "42"
    42 fortyTwo                            "42"
    #(1 2 3 4 5) fortyTwo                  "100"

メソッドは用意するけど、実行はさせたくないといった抽象クラスとしての定義をしたいこともあります。
例えば、SmalltalkオブジェクトをJSONに変換することを考えてみましょう。

JSONには ``null`` , ``true`` , ``false`` , ``number`` , ``string`` , ``array`` , ``object`` といったデータ型があり、
それぞれ ``null`` は ``nil`` , ``true`` と ``false`` は同じく ``true`` と ``false`` ,
``number`` は ``Number`` 、 ``string`` は ``String`` と ``UnicodeString`` 、 ``array`` は ``SequenceableCollection`` 、 ``object`` は ``Dictionary`` 相当です。

オブジェクトをJSONに変換するためにそれぞれのクラスに対して ``asJSON`` メソッドを定義しても良いのですが、
ユーザ定義クラスでもJSONに変換できるような仕組みにするために、Objectクラスに ``asJSON`` メソッドを追加定義します。

.. code-block:: st

    Object extend [
        asJSON [
            self subclassResponsibility
        ]
    ]

すると、どのオブジェクトからでも ``asJSON`` メソッドを実行できるようにはなりますが、
実際は実行させたくないので、 ``self subclassResponsibility`` と書いておきます。
これによって、 ``asJSON`` を呼び出したときのデフォルトの動作が
「このオブジェクトでは呼び出せないけど、サブクラスでは呼び出しできるかも」
ということを示すsubclassResponsibility例外を起こすようになります。

オブジェクトが ``nil`` の場合は ``'null'`` に変換したいので ``UndefinedObject`` の ``asJSON`` には、

.. code-block:: st

    UndefinedObject extend [
        asJSON [
            ^'null'
        ]
    ]

文字列 ``'null'`` を返すようなコードにしておけば、
subclassResponsibility例外を起こすことなく文字列 ``'null'`` を得られるようになります。
JSON出力にはほかにも ``true`` , ``false`` , 文字列、数値、配列、オブジェクトに対する定義も必要ですがここでは割愛します。
同様にサブクラスで ``asJSON`` の挙動を書き換えるコードを実装すれば、うまく動くはずです。

``subclassResponsibility`` のほかにもクラス定義用の例外があり、 ``notYetImplemented`` は未実装を表す例外で、
``shouldNotImplement`` は元々あった定義を潰すための例外です。

厳格なスタックを作ろうと ``OrderedCollection`` の定義を流用して ``push`` , ``pop`` , ``top`` 操作しかクラスを作ろうとしたときに、
``OrderedCollection`` の各メソッドを ``shouldNotImplement`` で使えなくするコードは次のようになるでしょう。

.. code-block:: none

    OrderedCollection subclass: Stack [
        addFirst: newObject [
            self shouldNotImplement
        ]

        add: newObject afterIndex: i [
            self shouldNotImplement
        ]

        (たくさんあるので略)
    ]


クラスの表示方法を変える
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``Object`` では ``printNl`` や ``displayNl`` が定義されているので、任意のオブジェクトの文字列表現を出力可能です。
これはユーザ定義のクラスでも同じですが、試しに出力してみると非常に楽しくない結果が表示されます。
試しに色情報を扱う ``RGB`` クラスを作って挙動を見てみます。

.. code-block:: st

    Object subclass: RGB [
        | r g b |
        RGB class >> red: r green: g blue: b [
            ^super new red: r green: g blue: b; yourself
        ]

        red [^r]
        red: value [r := value]

        green [^g]
        green: value [g := value]

        blue [^b]
        blue: value [b := value]
    ]

定義はこんなものでしょうか。本当はRGBの値の範囲もチェックしなければいけませんが省略します。
ではインスタンスを作り ``printNl`` をしてみましょう。

.. code-block:: st

    (RGB red: 255 green: 0 blue: 255) printNl      "a RGBと出力"

``a RGB`` と出力されました。もしクラスが母音から始まるのであれば、 ``an ARGB`` のように表示されます。
クラス名が表示されたのはいいのですが、RGBのようなクラスでは値が簡単に確認できると便利なので、
できれば出力形式を変えて`RGB`の各値を表示したいところです。

それにはクラスに自分自身の表現方法を教えてあげる必要があります。

.. code-block:: st

    printOn: aStream [
        aStream nextPutAll: '(%1, %2, %3)' % {r. g. b}
    ]

こんな定義をクラスに追加すると……？

.. code-block:: st

    (RGB red: 255 green: 0 blue: 255) printNl      "(255, 0, 255)と出力"

うまくいきました。``printNl`` の表示を変えるために ``printOn:`` を再定義しているのはちょっと奇妙に感じるかも知れませんが、
``printNl`` は内部的に ``printOn:`` を呼び出しているので、このコードで正しく動作します。
``printOn:`` は引数にストリームを受け取り、 ``printNl`` から呼び出されるときは引数として標準出力を渡すような構造になっています。


ファイナライザ
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

オブジェクトのメモリが破棄されるときに実行されるコードをファイナライザと呼びます。
類似概念であるデストラクタと違い、
ガーベジコレクタによってオブジェクトが使用しているメモリが解放されるときに実行されるので、必ずしも実行されるとは限りません。

データベースやシリアル通信などコネクションに限界があるインタフェースで利用する際にファイナライザ任せにしていると、
リソースを使い果たすこともあり得るので注意が必要です。
基本的にはいつ実行されてもいいような処理を記述します。

ファイナライザはどのオブジェクトにも用意されていて、実際の挙動は ``finalize`` メソッドで実装されています。
finalizeはデフォルトではなにもしません。
さらにオブジェクトは自動的に ``finalize`` を呼び出しもしません。

ファイナライザを呼び出すようにするには ``finalize`` メソッドを再定義して、 ``addToBeFinalized`` メソッドを呼び出す必要があります。
こうするとオブジェクトのメモリが回収されるときに自動的に ``finalize`` を呼び出すようになります。

.. code-block:: st

    Object subclass: Test [
        Test class >> new [
            ^super new addToBeFinalized; yourself
        ]

        finalize [
            Transcript showCr: 'finalized!'
        ]
    ]

試しにこの例の ``Test`` クラスをいくつか作ってみてもファイナライザは呼び出されませんが、
1000個10000個と作ればファイナライザが呼び出されることを確認できます。


ファイル入出力
--------------------------------------------------------------------------------

通常利用されるようなプログラムというのはプログラムファイル単一で動くものではなく、
ほかのファイルを読み書きすることが考えられます。
標準入出力も強力な機能を提供してくれますが、ファイル入出力は同時に複数のファイルを操作し、より柔軟な制御を可能とします。

ファイル入出力をするには、ファイルを開き、ファイルからデータを読み込みまたは書き込み、
ファイルを閉じるという手順を踏む必要があります。
具体的には、 ``FileStream#open:mode:`` を使います。 ``open:`` にファイル名、 ``mode:`` に開くモードを指定します。

モードはappend(追加書き込み), create(読み書き), read(読み込み), readWrite(読み書き), write(書き込み)の中から選択します。
createとwriteはファイルが存在しないときは新規作成し、存在する場合はファイルを空にします。

.. code-block:: st

    f := FileStream open: 'a.txt' mode: FileStream write

ファイルを開くのに成功すると、 ``FileStream`` のインスタンスが返ってくるので、
これを使ってファイルに対して読み書きの操作をおこないます。
逆にファイルを開くのに失敗すると ``FileStream#open:mode:`` はインスタンスを返す代わりに例外を発生させます。

ファイルから読み取るメソッドには、1文字読み取る ``next` , 複数文字読み取る ``next:`` ,
バイナリデータを読み取る ``nextSignedByte`` , ``nextShort`` , ``nextLong`` などがあります。

読み取りに失敗すると ``next`` は ``nil`` を返しますが、ほかのメソッドは例外を発生させます。

.. code-block:: st

    f := FileStream open: 'a.txt' mode: FileStream read.
    f next: 10.
    f close

書き込みには ``next`` と ``next:`` の逆バージョンである ``nextPut:`` と ``nextPutAll:`` があります。
大体の場合はこの2つのメソッドを使うことになるでしょう。
オブジェクトをそのまま書き込みたいときには、 ``Object#printOn:`` の引数に ``FileStream`` のインスタンスを渡すと
``nextPutAll:`` などをわざわざ使わなくともファイルに書き出すことができます。

.. code-block:: st

    f := FileStream open: 'a.txt' mode: FileStream write.
    f nextPutAll: 'Hello, world!'.
    42 printOn: f.
    f close


ObjectDumper
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

データをファイルに書き出して保存しておき、必要になったときに取り出す、というのはよくあることです。むしろ普通のことです。

単純なテキストや数値が書かれている.iniファイルのようなファイルとのやりとりであれば、
データを書き出したり、読み出して文字列や数値の再構成をするのは容易です。
でも保存したいデータが複雑になってきたら？保存しようと思うデータが配列で要素に辞書があって、
その中には自作クラスが可能されていて、自作クラスの中では別の自作クラスのインスタンスが使われている……なんてことになったら、
単にデータを書き出すだけでも永続化(シリアライズ)の方法を念入りに考えなければならず一苦労です。

gstではこの永続化の問題の解決策として ``ObjectDumper`` クラスを用意しています。
永続化するデータをgst以外のシステムから利用しないのであればこれが一番簡単な保存方法です。

``ObjectDumper`` はストリームとオブジェクトを受け取り永続化をします。
オブジェクトはユーザ定義のものでも構わず、制限はありません。
ファイルに書き込むときは書き込み用のストリームを、読み込むときは読み込み用のストリームを渡すだけです。

.. code-block:: st

    f := FileStream open: 'a.txt' mode: FileStream write.
    d := ObjectDumper on: f.
    d dump: 42.
    f close

``ObjectDumper#on:`` はストリームを取りインスタンスを返します。
インスタンスに対して ``dump:`` でオブジェクトを書き出し、 ``load:`` で読み込みをします。
読み込みに失敗したときは ``nil`` を返します。または簡単にクラスメソッド ``ObjectDumper#dump:to:`` だけで、

.. code-block:: st

    f := FileStream open: 'a.txt' mode: FileStream write.
    ObjectDumper dump: 42 to: f.
    f close

と書くこともできます。逆に読み込みは ``ObjectDumper#loadFrom:`` になります。


例外処理
--------------------------------------------------------------------------------

例外を受ける
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

コードの実行に失敗するとほとんどの場合は例外という形で、そのコードの何が悪かったのかを教えてくれます。
例えば0除算をすると……

.. code-block:: st

    1 // 0
    "
    Object: 1 error: The program attempted to divide a number by zero
    ZeroDivide(Exception)>>signal (ExcHandling.st:254)
    "

0で割ろうとしたことを検知して、システムはZeroDivide例外を発生させました。
例外が発生するということはコード上重大な不具合であることが多いので、
例外を放っておいてプログラムの実行を自動的に打ち切るという戦法も取れますが、
実行してみてダメだったら別の方法を取りたいときには、例外を拾ってプログラムの実行を継続したいことがあります。

特に拾いたい例外を決め打ちしないのであれば、 ``BlockClosure#ifError:`` を使います。
今の0除算のコードを修正して、もし0で割ろうとしたら0を返すようなコードに変えてみましょう。

.. code-block:: st

    [1 // 0] ifError: [0]                   "0"

``ifError:`` のレシーバとなるブロックはすぐに実行され、
ブロック内で例外が発生すれば ``ifError:`` の引数であるブロックの実行結果、
例外が発生しなければレシーバの実行結果を返すようになっています。

特定の例外について処理をしたいときには、 ``on:do:`` が使えます。

.. code-block:: st

    [1 // 0 + 5] on: ZeroDivide do: [0]                              "5"

``on:do:`` では ``on:`` に指定した例外だけを拾います。
``on:do:`` から ``on:do:on:do:on:do:on:do:on:do:`` の5例外までのメソッドが用意されています。

ある地点で ``do:`` に与えたブロックの実行をすぐに打ち切りたいときには ``return`` と ``return:`` が使えます。
``return`` は ``nil`` を、 ``return:`` は指定したオブジェクトを文の評価値として返します。

.. code-block:: st

    [1 // 0 + 5] on: ZeroDivide do: [:e | e return: 0. 100]          "0"
    [1 // 0 + 5] on: ZeroDivide do: [:e | 0]                         "0"

レシーバブロックの再実行というちょっと変わった機能もあり、 ``retry`` はレシーバブロックの再実行、
``retryUsing:`` は与えられたブロックを実行します。

.. code-block:: st

    [1 // 0 + 5] on: ZeroDivide do: [:e | e retry]                   "無限ループ!"
    [1 // 0 + 5] on: ZeroDivide do: [:e | e retryUsing: [100]]       "100"

``retry`` は簡単に無限ループを作りがちなので注意が必要でしょう。
また、 ``resume:`` を呼び出すと例外発生部分を特定のオブジェクトに変更して再度レシーバブロックを実行できます。

.. code-block:: st

    [1 // 0 + 5] on: ZeroDivide do: [:e | e resume: 0]               "5"

``1 // 0 + 5`` でZeroDivideが起きるのは ``1 // 0`` なので、
この場所を ``e resume: 0`` によって ``0`` に置き換えて再計算し、 ``0 + 5`` を返すようになります。


例外を起こす
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

単純にエラーという形で例外を起こすのであれば、 ``error:`` や ``halt:`` にメッセージをつけて呼び出します。
オブジェクトがこのメッセージを受け取ると、 ``Error`` または ``Halt`` クラスを使って例外を起こします。

.. code-block:: st

    self error: 'something wrong'   "Object: nil error: something wrong"

``error:`` も ``halt:`` も単純なエラーを示す例外ですが、 ``error:`` は ``resume:`` による復帰が不可能です。

.. code-block:: st

    [self error: 'something wrong'] on: Error do: [:e | e resume: 'resumed!']
    "Object: nil error: Exception not resumable - #resume: failed"

.. code-block:: st

    [self halt: 'something wrong'] on: Halt do: [:e | e resume: 'resumed!']
    "'resumed!'"

自作の例外を起こすには ``Exception`` クラスを継承することになります。
エラー文も変えたい場合は ``messageText`` または ``messageText:`` を上書きする必要があります。

.. code-block:: st

    Exception subclass: MyError [
        messageText: aString [
            ^super messageText: 'MyError called: ' , aString
        ]
    ]

    MyError signal: 'test'       "Object: nil error: MyError called: test"

また、組み込みの例外も多く実装されています。主にシステムの方で使われるので ``SystemExceptions`` 名前空間で定義されています。

.. code-block:: st

    SystemExceptions.InvalidValue signalOn: nil reason: 'should not be nil'


名前空間
--------------------------------------------------------------------------------

名前空間を使うには ``Namespace`` クラスを使い、新たに導入したい名前を指定するだけです。

.. code-block:: st

    Namespace current: FooBar [
        Object subclass: DeepThought [
            DeepThought class >> answer [^42]
        ]
    ]

これで名前空間 ``FooBar`` とその中に ``DeepThought`` クラスができました。
``DeepThought`` クラスにアクセスするには、名前空間を ``DeepThought`` の頭につけて、

.. code-block:: st

    FooBar.DeepThought answer                         "42"

とします。
名前空間名を頭につけるのが面倒なときは、インポート文を書くとそのスコープで名前空間名を書かなくて良くなります。

.. code-block:: st

    Namespace current: FooBar [
        Object subclass: DeepThought [
            DeepThought class >> answer [^42]
        ]
    ]

    Object subclass: Test [
        <import: FooBar>
        Test class >> run [
            DeepThought answer printNl                "(FooBarは必要ない)"
        ]
    ]

    Test run                                          "42"


Cとの連携
--------------------------------------------------------------------------------

gstからCを呼び出す
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

gstではFFI(Foreign Function Interface)によってCのライブラリ呼び出しをサポートしています。
共有ライブラリがあれば、ラッパープログラムなしにライブラリを読み込んで関数を呼び出すことができます。

試しに簡単な共有ライブラリを作ってみましょう。簡単のため、引数として与えた値を2倍するだけのCの関数を書きます。

.. code-block:: c

    int twice(int n) {
        return n * 2;
    }

この関数の動作は明快でCをちょっと齧ったことがあれば、整数nをただひとつ引数にとって、nの2倍の数を整数として返す、
というのがわかると思います(そして驚くほど退屈です)。
この関数にどうにかSmalltalkの整数値を流し込んで2倍した値を取れればミッション成功です。

ミッションを成功させるためには共有ライブラリを作らなければいけませんが、
``gcc`` や ``clang`` でコンソールから次のコマンドを入力すればおよそ共有ライブラリを作成できます。

.. code-block:: bash

    $ gcc -o twice.so twice.c -O2 -fPIC -shared

``twice.so`` という名前の共有ライブラリができるので、あとはSmalltalk側から呼び出すだけです。
Cの関数を呼び出すにはまず共有ライブラリを ``DLD#addLibrary:`` で読み込む必要があります。

.. code-block:: st

    DLD addLibrary: './twice'

``.so`` , ``.sl`` , ``.a`` , ``.dll`` などの拡張子は自動的に付加されるので省略可能です。
共有ライブラリを読み込んだら、共有ライブラリ内の関数をgst内で対応づけるためのメソッドを用意する必要があるので、
適当なクラスとメソッドを作ります。呼び出しは引数の型に一致するオブジェクトを渡すだけです。

.. code-block:: st

    DLD addLibrary: './twice'.

    Object subclass: MyLib [
        MyLib class >> twice: n [
            <cCall: 'twice' returning: #int args: #(#int)>
        ]
    ].

    MyLib twice: 100               "200"

``cCall`` は共有ライブラリの関数をgstのメソッドに関連づける宣言で、
``cCall:`` に関数名、 ``returning:`` に返り値の型、 ``args:`` に引数の型の配列を指定します。
``returning:`` には次の型を指定できます。

.. code-block::

    型指定                 Cの型                   型指定                 Cの型
    #boolean              int                    #wchar                wchar_t
    #char                 char                   #wstring              wchar_t *
    #uChar                unsigned char          #wstringOut           wchar_t *
    #short                short                  #unknown              ?
    #uShort               unsigned short         #symbol               char *
    #int                  int                    #symbolOut            char *
    #uInt                 unsigned int           #byteArray            char *
    #long                 long                   #byteArrayOut         char *
    #uLong                unsigned long          #void                 void
    #longLong             long long              #cObject              void *
    #uLongLong            unsigned long long     #cObjectPtr           void *
    #float                float                  #smalltalk            OOP
    #double               double                 #selfSmalltalk        OOP
    #longDouble           long double            #variadic             ?
    #string               char *                 #variadicSmalltalk    OOP
    #stringOut            char *

``#string`` と ``#stringOut`` はCの型では同じに見えますが、ちょっとした違いがあります。
``returning:`` に ``#string`` を指定したときは単にC側の文字列から単純にgstの ``String`` が生成されるだけですが、
``#stringOut`` を指定したときは`String`を生成してさらに文字列のメモリ解放をします。

呼び出す関数によっては ``#stringOut`` を指定してメモリリークを防ぐ必要があります。
例えばC標準ライブラリの ``string.h`` ヘッダファイルで定義されている ``strdup(3)`` は

.. code-block:: c

    char *strdup(const char *s)

の関数定義を持ち、この関数を実行すると引数`s`の複製である新しい文字列へのポインタを返します。
新しい文字列は ``strdup`` 側で自動的に確保されて、メモリの解放はプログラマに任されています。 ``strdup(3)`` に対して

.. code-block:: st

    <cCall: 'strdup' returning: #string args: #(string)>

と定義するとメモリが解放されないので呼び出すたびにメモリがリークしていきます。
正しくは ``returning:`` に ``#stringOut`` を指定しなければなりません。
``args:`` に ``#stringOut`` を指定したときは、書き換え可能オブジェクトとして元々の値が破壊されることがあります。

共用体と構造体は ``CUnion`` と ``CStruct`` のサブクラスを作ることで実現できます。
次のコードで示す構造体はCの ``time.h`` ヘッダファイルで定義されている ``struct tm`` のgst版です。
型指定の場合は ``{Tm}`` になります。呼び出し時には構造体へのポインタが渡されることになります。

.. code-block:: none

    CStruct subclass: Tm [
        <declaration: #(
            (#sec    #int)
            (#min    #int)
            (#hour   #int)
            (#mday   #int)
            (#mon    #int)
            (#year   #int)
            (#wday   #int)
            (#yday   #int)
            (#isdst  #int)
        )>
    ]

構造体メンバの宣言でポインタ表現は ``(#ptr #long)`` のように、固定長配列表現は ``(#array #char 8)`` のように宣言できます。

構造体Tmを使った日時取得の例を次に示します。 ``Tm`` のインスタンスを作るには ``new`` を呼び出します。
Cの変数宣言と同じようにメンバの値は特定の値に初期化されません。
文字列などのポインタを含むメンバを初期化せずに ``inspect`` しようとしたりすると、うまくいかないことがあります。

メンバはFFI用に ``CObject`` のサブクラスになっていて通常のオブジェクトとは異なるため、
値を参照・設定をするには ``value:`` , ``value:`` メソッドを使います。

.. code-block:: st

    t := Tm new.
    t hour value: 0.
    t min value: 0.
    t sec value: 0

構造体 ``Tm`` を使って正しく日時情報を取得できるかは ``time(2)`` と ``localtime(3)`` を呼び出してみると分かります。
この2つの関数の定義は、

.. code-block:: c

    time_t time(time_t *t);
    struct tm *localtime(const time_t *timep);

となっています。 ``time(2)`` はUNIXタイムスタンプを返します。
引数が ``NULL`` でなければ引数が指すアドレスにも格納します。
``localtime(3)`` は ``time_t`` のポインタを取り、現在のロケールにおける日時情報を返します。

``time(2)`` と ``localtime(3)`` をgstで呼び出すには次のような定義になるでしょう。

.. code-block:: st

    DLD addLibrary: 'libc.so.6'.                       "または単純にlibc"

    Object subclass StandardTime [
        StandardTime class >> time: t [
            <cCall: 'time' returning: #long args: #(#cObject)>
        ]

        StandardTime class >> localtime: t [
            <cCall: 'localtime' returning: #{Tm} args: #(#cObject)>
        ]
    ]

ポインタ型に対応するのは ``#cObject`` です。
``time_t`` は実際どのような定義になっているのかは処理系に依存しますが、今回は ``long`` としました。

まずは ``time(2)`` を呼び出して現在のUNIXタイムスタンプを取得してみます。

.. code-block:: st

    now := StandardTime time: nil          "1479537778"

次に ``localtime(3)`` を呼び出すのですが、
``localtime(3)`` はUNIXタイムスタンプへのポインタを取るので ``now`` の値をそのまま渡すこはできません。
``CObject`` のサブクラスである ``CLong`` で包んで渡します。

.. code-block:: st

    nowPtr := CLong gcValue: now

これで ``nowPtr`` は ``long`` 値 ``now`` を指しているポインタで引数の ``#cObject`` と合致する引数になりました。
あとは ``nowPtr`` を使って ``localtime(3)`` を呼び出せば時刻情報を取得できるはずです！

.. code-block:: st

    tm := StandardTime localtime: nowPtr.
    tm inspect
    "Tm(16r7FF56B000290)
        sec: 16
        min: 19
        hour: 22
        mday: 17
        mon: 10
        year: 116
        wday: 4
        isdst: 321"

もちろんのことですが、これはFFIによる時刻取得の例であって、
gstではこうしないと時刻情報を取得できないわけではありません。
より一般的で簡単に時刻情報にアクセスするには ``DateTime`` クラスを使うのが良い方法です。


Cからgstを呼び出す
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

gstはCから呼び出すことができます。呼び出しやすさで言えば、Cとの連携を第一に考えて設計されているLuaと比較しても簡単です。
gstはC側のインタフェースとして6つの関数を用意しています。


.. code-block:: c

    OOP msgSend(OOP receiver, OOP selector, ...);
    OOP strMsgSend(OOP receiver, const char *selector, ...);
    OOP vmsgSend(OOP receiver, OOP selector, OOP *args);
    OOP perform(OOP receiver, OOP selector);
    OOP performWith(OOP receiver, OOP selector, OOP arg);
    OOP evalExpr(const char *str);
    OOP evalCode(const char *str);
    OOP msgSendf(PTR resultPtr, const char *fmt, ...);

gstのオブジェクトは ``OOP`` オブジェクトとして扱われ、インタプリタプロキシを通じてコードの実行をします。
インタプリタプロキシは ``gstpub.h`` で宣言されており、
環境にもよりますが ``/usr/local/include/gstpub.h`` のような場所にインストールされているはずです。

それぞれの関数の使い方は難しいものではありません。
CからSmalltalkのオブジェクトへの変換はプロキシ関数によって自動で行われるので、特に考える必要はありません。

``msgSend()`` はレシーバ、セレクタを最初の2引数として与え、残りのオプション引数にセレクタの引数を指定します。
引数の終わりには ``NULL`` を指定する必要があります。

.. code-block:: c

    VMProxy *gst = &gst_interpreter_proxy;
    OOP x = gst->msgSend(gst->intToOOP(10), gst->symbolToOOP("raisedTo:"), gst->intToOOP(2), 0);
    printf("%ld\n", gst->OOPToInt(x));                      /* 100 */

``strMsgSend()`` は ``msgSend()`` に似ていますが、セレクタに直接Cの文字列を与えることができる、 ``msgSend()`` のショートカット関数です。

.. code-block:: c

    OOP x = gst->msgSend(gst->intToOOP(10), "between:and:", gst->intToOOP(0), gst->intToOOP(100), 0);
    printf("%d\n", gst->OOPToBool(x));                      /* 1 */

``vmsgSend()`` は ``OOP`` の配列を取るパターンです。

.. code-block:: c

    OOP args[2] = {gst->intToOOP(5), 0};
    OOP x = gst->vmsgSend(gst->intToOOP(2), "*", args);
    printf("%ld\n", gst->OOPToInt(x));                      /* 10 */

``perform()`` と ``performWith()`` は単項メッセージのショートカット関数です。

.. code-block:: c

    OOP x = gst->performWith(gst->intToOOP(255), gst->symbolToOOP("bitXor:"), gst->intToOOP(42));
    printf("%ld\n", gst->OOPToInt(x));                      /* 213 */

``evalExpr()`` と ``evalCode()`` は単純に与えた文字列をSmalltalkのコードとして実行します。 ``evalCode()`` は値を返しません。

``msgSendf()`` は ``printf(3)`` のような文字列フォーマットを使用して、
自動的にCとSmalltalk間のデータ変換をします。計算結果の代入先である第1引数が`NULL`のとき、計算結果は捨てられます。

.. code-block:: c

    VMProxy *gst = &gst_interpreter_proxy;
    int result; // [返り値の型] [メッセージング] で指定
    OOP x = gst->msgSendf(&result, "%i %i + %i", 1, 2);
    printf("%d\n", result);                                 /* 3 */

``msgSendf()`` に渡すデータは識別子に依存し、以下の種類があります。

.. code-block::

    指定子          Cの型            Smalltalkのオブジェクト     変換関数
    %i             long            Integer                   intToOOP
    %f             double          Float                     floatToOOP
    %F             long double     Float                     longDoubleToOOP
    %b             int             Boolean                   boolToOOP
    %B             OOP             BlockClosure
    %c             char            Character                 charToOOP
    %C             PTR             CObject                   cObjectToOOP
    %s             char *          String                    stringToOOP
    %S             char *          Symbol                    symbolToOOP
    %o             OOP             (any)
    %t             char *, PTR     CObject
    %T             OOP, PTR        CObject
    %w             wchar_t         Character                 wcharToOOP
    %W             wchat_t *       UnicodeString             wstringToOOP
    %v             (値を無視)

``%t`` と ``%T`` を指定したときには、2つの引数が必要になり、1つ目に型、2つ目にアドレスを指定します。
``%t`` では1つ目に指定した文字列が ``typeNameToOOP()`` に渡され、 ``%T`` では直接 ``OOP`` 型を指定します。

.. code-block:: c

    int x, a = 42;
    int y, b = 58;
    OOP type = gst->typeNameToOOP("CIntType");
    gst->msgSendf(&x, "%i %t value", "CIntType", &a);
    gst->msgSendf(&y, "%i %T value", type, &b);
    printf("x = %d, y = %d\n", x, y);                       /* x = 52, y = 58 */

関数の実行に失敗したときには、結果の型の応じて ``0`` または ``NULL`` が結果となります。


おまけ: gstからLuaを呼び出す
--------------------------------------------------------------------------------

Luaもgst同様にCのインタフェースを用意しているので、それを直接呼び出せばgstから直接Luaも実行できてしまいます。

Luaの ``luaL_dostring()`` 関数を使ってLuaコードを実行するには、次のコードを書きます。
libluaは開発用(devel)パッケージをインストールしていないと見つからないかもしれません。

.. code-block:: none

    DLD addLibrary: 'liblua'.

    Object subclass: Lua [
        | lua |
        Lua class >> new [
            ^super new addToBeFinalized; initialize; yourself
        ]

        luaNewstate [
            <cCall: 'luaL_newstate' returning: #cObject args: #()>
        ]

        luaClose: l [
            <cCall: 'luaL_close' returning: #void args: #(#cObject)>
        ]

        luaOpenlibs: l [
            <cCall: 'luaL_openlibs' returning: #void args: #(#cObject)>
        ]

        lua: l loadString: s [
            <cCall: 'luaL_loadstring' returning: #int> args: #(#cObject #string)>
        ]

        lua: l pCallArgs: args results: results message: message context: context function: function [
            <cCall: 'lua_pcallk' returning #int args: #(#cObject #int #int #int #int #cObject)>
        ]

        initialize [
            lua := self luaNewstate.
            self luaOpenlibs: lua
        ]

        finalize [
            self luaClose: lua
        ]

        doString: code [
            ^(self lua: lua loadString: code) > 0 or: [
                self lua: lua pCallArgs: 0 results: -1 message: 0 context: 0 function: nil
            ]
        ]
    ]

    l := Lua new.
    l doString: 'print("Hello, world!")'        "Hello, world!"
