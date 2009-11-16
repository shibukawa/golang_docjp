.. _go_tutorial:

========
Let's Go
========

.. Introduction

イントロダクション
==================

.. This document is a tutorial introduction to the basics of the Go programming language, intended for programmers familiar with C or C++. It is not a comprehensive guide to the language; at the moment the document closest to that is the language specification. After you've read this tutorial, you might want to look at Effective Go, which digs deeper into how the language is used. Also, slides from a 3-day course about Go are available: Day 1, Day 2, Day 3.

このドキュメントはGoプログラミング言語の基本を紹介するチュートリアルです。CやC++に親しんだプログラマを対象にしています。このドキュメントは包括的なドキュメントではありません。そのような目的に一番近いのは言語仕様のドキュメントになります。このチュートリアルを読むと、言語をどのように使うのかという詳細を掘り下げるために、次にEffective Goが読みたくなるでしょう。また、Goに関する3日コースのスライドも入手することができます。 `1日目 <http://golang.org/doc/GoCourseDay1.pdf>`_ `2日目 <http://golang.org/doc/GoCourseDay2.pdf>`_ `3日目 <http://golang.org/doc/GoCourseDay3.pdf>`_

.. The presentation here proceeds through a series of modest programs to illustrate key features of the language. All the programs work (at time of writing) and are checked into the repository in the directory /doc/progs/.

このプレゼンテーションの中のシンプルなサンプルプログラムを通して、この言語のキーとなる機能について、説明していきます。すべてのプログラムは自分で書いてみても動作しますし、 :file:`/doc/progs/` の中に置いてあります。

.. Program snippets are annotated with the line number in the original file; for cleanliness, blank lines remain blank.

プログラムのコードはオリジナルのファイルの行番号付きで表示しています。見やすさのために、空行はブランクのまま残してあります。

Hello, World
============

.. Let's start in the usual way:

それでは、いつもの入門コードから始めます:

.. code-block:: cpp

  05    package main

  07    import fmt "fmt"  // フォーマット付き入出力を実装したパッケージ
  
  
  09    func main() {
  10        fmt.Printf("Hello, world; or Καλημ?ρα κ?σμε; or こんにちは 世界\n");
  11    }

.. 07    import fmt "fmt"  // Package implementing formatted I/O.

.. Every Go source file declares, using a package statement, which package it's part of. It may also import other packages to use their facilities. This program imports the package fmt to gain access to our old, now capitalized and package-qualified, friend, fmt.Printf.

すべてのGoのソースファイルは、そのファイルが属しているパッケージを宣言するために、package文の宣言を行います。また、他のパッケージに含まれる機能を利用するために、他のパッケージをインポートすることもあります。このプログラムでは、パッケージ名付きで先頭の大文字化はされているという違いはありますが、C/C++プログラマにはおなじみの、古くからの友人である、\ :func:`fmt.Printf`\ を使用するために、\ :mod:`fmt`\ パッケージをインポートしています。

.. Functions are introduced with the func keyword. The main package's main function is where the program starts running (after any initialization).

funcキーワードを使用することで、関数を定義することが出来ます。mainパッケージのmain関数は、いくつかの初期化の後に最初に呼ばれるプログラムの入り口になります。

.. String constants can contain Unicode characters, encoded in UTF-8. (In fact, Go source files are defined to be encoded in UTF-8.)

文字列定数はUTF-8でエンコードされていて、ユニコード文字を含むことができます。実際に仕様上でも、GoのソースファイルはUTF-8でエンコードされると定義されています。

.. The comment convention is the same as in C++::

コメントの書き方は、C++で行われている慣習と同じです。

::

    /* ... */
    // ...

.. Later we'll have much more to say about printing.

後の方の説明では、画面表示に関しても、もっと詳しく説明していきます。

.. Compiling
   =========

コンパイル
==========

.. Go is a compiled language. At the moment there are two compilers. Gccgo is a Go compiler that uses the GCC back end. There is also a suite of compilers with different (and odd) names for each architecture: 6g for the 64-bit x86, 8g for the 32-bit x86, and more. These compilers run significantly faster but generate less efficient code than gccgo. At the time of writing (late 2009), they also have a more robust run-time system although gccgo is catching up.

Goはコンパイル型言語です。現状のところコンパイラが2つあります。GccgoはGCCバックエンドを利用するコンパイラです。またアーキテクチャーごとに異なる(そしておかしな)名前のコンパイラーが一式あります。64-bit x86用の6g、32-bit x86用の8などです。これらはとても高速ですが、gccgoより非効率なコードを生成します。執筆時点(2009後半)では、gccgoも追い上げていますが、より堅牢なランタイムシステムを持ちます。

.. Here's how to compile and run our program. With 6g, say:

プログラムをコンパイルして走らせる方法を紹介します。6gだと

.. code-block:: bash

    $ 6g helloworld.go  # コンパイル; オブジェクトはhelloworld.6となる
    $ 6l helloworld.6   # リンク; 出力は6.outとなる
    $ 6.out
    Hello, world; or Καλημ?ρα κ?σμε; or こんにちは 世界
    $

..  $ 6g helloworld.go  # compile; object goes into helloworld.6
    $ 6l helloworld.6   # link; output goes into 6.out

.. With gccgo it looks a little more traditional:

gccgoだともう少し従来のやりかたに似ています。

.. code-block:: bash

    $ gccgo helloworld.go
    $ a.out
    Hello, world; or Καλημ?ρα κ?σμε; or こんにちは 世界
    $

Echo
====

.. Next up, here's a version of the Unix utility echo(1)::

次は、Unixコマンドのecho(1)と同じ動作をするサンプルです。

.. code-block:: cpp

   05    package main
   07    import (
   08        "os";
   09        "flag";  // command line option parser
   10    )
   12    var omitNewline = flag.Bool("n", false, "don't print final newline")
   14    const (
   15        Space = " ";
   16        Newline = "\n";
   17    )
   19    func main() {
   20        flag.Parse();   // Scans the arg list and sets up flags
   21        var s string = "";
   22        for i := 0; i < flag.NArg(); i++ {
   23            if i > 0 {
   24                s += Space
   25            }
   26            s += flag.Arg(i)
   27        }
   28        if !*omitNewline {
   29            s += Newline
   30        }
   31        os.Stdout.WriteString(s);
   32    }


.. This program is small but it's doing a number of new things. In the last example, we saw func introduce a function. The keywords var, const, and type (not used yet) also introduce declarations, as does import. Notice that we can group declarations of the same sort into parenthesized, semicolon-separated lists if we want, as on lines 7-10 and 14-17. But it's not necessary to do so; we could have said::

このプログラムは小さいですが、多くの新しいことが出てきます。 最後の例で、"func"を確認しました。 今回は、"var", "const", "type(まだ使いませんが)", パッケージ利用前に宣言する"import"を使用します。 注意点として、7-10, 14-17行目のように、セミコロンで区切りことにより、同じ種類の宣言を並べて記述することができます。 しかし、必ずしもそう記述する必要はありません。 次のコードでも良いと言ったのですが、

.. code-block:: cpp

   const Space = " "
   const Newline = "\n"

.. Semicolons aren't needed here; in fact, semicolons are unnecessary after any top-level declaration, although they are needed as separators within a parenthesized list of declarations.

セミコロンはここで必要ではありません。 事実上、セミコロンはどんなトップレベル宣言の後にも不要です。 "()"を利用し複数宣言する場合に区切り文字として必要です。

.. You can use semicolons just the way you would in C, C++, or Java, but if you prefer you can also leave them out in many cases. They separate statements rather than terminate them, so they aren't needed (but are still OK) at the end of the last statement in a block. They're also optional after braces, as in C.

C、C++、またはJavaと同じようにセミコロンを使用できますが、多くの場合、れらを省くことができます。セパレータステートメントの処理の前に処理自体を切り離すため、ブロックの最後にセミコロンは必要ではありません （まだOKです）。Cのように、ブレス後につけることは任意です。

.. Have a look at the source to echo. The only necessary semicolons in that program are on lines 8, 15, and 21 and of course between the elements of the for loop on line 22. The ones on line 9, 16, 26, and 31 are optional but are there because a semicolon on the end of a list of statements makes it easier to edit the list later. 

echoサンプルのソースコードを見てみましょう。プログラム中にセミコロンが必要な箇所は、8,15,21行目にあり、22行目は、forループの要素間の記述のため必要です。9,16,26,31行目のものは任意ですが、リストの最後、ステートメントの最後が簡単にわかるようにするために付けています。


.. This program imports the "os" package to access its Stdout variable, of type `*os.File.`  The import statement is actually a declaration:  in its general form, as used in our ``hello world'' program, it names the identifier (fmt) that will be used to access members of the package imported from the file ("fmt"), found in the current directory or in a standard location. 
.. In this program, though, we've dropped the explicit name from the imports;  by default, packages are imported using the name defined by the imported package, which by convention is of course the file name itself. 
.. Our ``hello world'' program could have said just import "fmt".

このプログラムは、Stdout変数にアクセスするために、"os"をインポートしています。, of type `*os.File.` "import"句は、パッケージを利用する場合に明示的に宣言します。 
"hello,world"のサンプルでは、見つけられたファイル("fmt")からインポートされたパッケージのメンバーにアクセスするための識別子を(fmt)と命名しています。インポート対象は、カレントディレクトリか、標準の位置にある必要があります。
もっとも、今回のサンプルでは、インポート時に明白な名前を落としました。
識別子を指定しない場合は、ファイル名を利用してインポートします。
"hello, world"のサンプルでは、明示して、"fmt"パッケージをインポートしています。

.. You can specify your own import names if you want but it's only necessary if you need to resolve a naming conflict.

必要であれば、パッケージ名の競合を解決するために名前を指定することができます。

.. Given os.Stdout we can use its WriteString method to print the string.

os.Stdout の WriteString メソッドを利用することで、文字列を出力することができます。

.. Having imported the flag package, line 12 creates a global variable to hold the value of echo's -n flag. The variable omitNewline has type *bool, pointer to bool.

12行目では、flagパッケージをインポートし、"-n"が実行時に与えられたかを保持するグローバル変数を作成しています。 　omitNewline変数は、*bool型で、boolへのポインタです。

.. In main.main, we parse the arguments (line 20) and then create a local string variable we will use to build the output.

mainメソッドの中では、20行目に実行時引数を分析して、次に、出力用のローカル変数を作成します。

.. The declaration statement has the form::

変数の宣言は以下のようになります。

.. code-block:: cpp

   var s string = "";

.. This is the var keyword, followed by the name of the variable, followed by its type, followed by an equals sign and an initial value for the variable.

var キーワードに続き、変数名、変数の型、=に続き初期化する値を記述します。

.. Go tries to be terse, and this declaration could be shortened. Since the string constant is of type string, we don't have to tell the compiler that. We could write::

Go は簡潔になろうとしています、変数の宣言ではそれができました。string型は、文字列定数なので、コンパイラに通知する必要がありません。次のように記述することもできます。

.. code-block:: cpp

   var s = "";

.. or we could go even shorter and write the idiom::

変数は以下のように、もっと短く記述することもできます。

.. code-block:: cpp

   s := "";

.. The := operator is used a lot in Go to represent an initializing declaration. There's one in the for clause on the next line::

:=オペレータは、初期化時にGoで大いに使用されます。 次の行の節でも1つでてきます。

.. code-block:: cpp

   22        for i := 0; i < flag.NArg(); i++ {

.. The flag package has parsed the arguments and left the non-flag arguments in a list that can be iterated over in the obvious way.

flagパッケージは、実行時引数を解析し、オプション引数以外をわかりやすく繰り返し取得できるリストにしてくれます。

.. The Go for statement differs from that of C in a number of ways. First, it's the only looping construct; there is no while or do. Second, there are no parentheses on the clause, but the braces on the body are mandatory. The same applies to the if and switch statements. Later examples will show some other ways for can be written.

Go の for文の書式は、Cのものと多く異なっています。 最初に、ループ構造です。 while文、do文がありません。
次に、節を示す括弧がありません。"()"、しかし 中括弧"{}"によるブレスが必要です。
これらは、if文、switch文にも当てはまります。 後述する例で示します。

.. The body of the loop builds up the string s by appending (using +=) the flags and separating spaces. After the loop, if the -n flag is not set, the program appends a newline. Finally, it writes the result.

プログラム中では、出力用変数 s に対して、スペースを区切り文字として追加しています。 このとき (+=)を使っています。
ループが完了後に、コマンドライン引数で、"-n"が指定されていなければ、改行を追加しています。最後に、結果を標準出力に出力しています。

.. Notice that main.main is a niladic function with no return type. It's defined that way. Falling off the end of main.main means ''success''; if you want to signal an erroneous return, call::

mainメソッドの戻り値が無い場合は、 niladicメソッドとなります。 戻り値がなくとも、その様に定義されます。 mainメソッドが終了すれば、"success"を意味します。 もし、エラー終了としたい場合は、以下の式を呼び出してください。

.. code-block:: cpp

   os.Exit(1)

.. The os package contains other essentials for getting started; for instance, os.Args is a slice used by the flag package to access the command-line arguments.

osパッケージはプログラムを開始するための基礎部分を含んでいます。 例えば、os.Argsは、コマンドライン引数へアクセスするために、flagパッケージを使っています。

..
   An Interlude about Types
   ========================

型
===

..
   Go has some familiar types such as ``int`` and ``float``, which represent
   values of the ''appropriate'' size for the machine. It also defines
   explicitly-sized types such as ``int8``, ``float64``, and so on, plus
   unsigned integer types such as ``uint``, ``uint32``, etc. These are distinct
   types; even if ``int`` and ``int32`` are both 32 bits in size, they are not
   the same type. There is also a ``byte`` synonym for ``uint8``, which is the
   element type for strings.

Go には多くの :ctype:`int` や :ctype:`float` のような型がありますが、
これらの値のサイズは実行するマシンで ''適切'' なサイズになります。このため、
サイズが明示的な型として :ctype:`int8`, :ctype:`float64` などの型や、
負数のない整数型といった :ctype:`uint`, :ctype:`uint32` なども定義されています。
型名が異なる型は、明確に別の型としてあつかわれるので、 :ctype:`int` と
:ctype:`int32` はどちらもサイズが32ビットですが、異なる型となります。
他に、 :ctype:`uint8` の別名である :ctype:`byte` という型があり、
これは文字列の要素のための型です。

..
   Speaking of ``string``, that's a built-in type as well. Strings are
   *immutable values* -- they are not just arrays of ``byte`` values. Once
   you've built a string *value*, you can't change it, although of course you
   can change a string *variable* simply by reassigning it. This snippet from
   ``strings.go`` is legal code::
 
:ctype:`string` 型という組み込み型もあります。文字列は *変更不可能な値* で、
単なる :ctype:`byte` 型の配列ではありません。一度、文字列型の値を作ると、
この値を変更する事は出来ず、文字列を変更は出来ますが実質的には、別の
文字列型の値が割り当てられることになります。以下は ``strings.go`` の
ソースコードの一部です。

.. code-block:: cpp

  11        s := "hello";
  12        if s[1] != 'e' { os.Exit(1) }
  13        s = "good bye";
  14        var p *string = &s;
  15        *p = "ciao";

..
   However the following statements are illegal because they would modify a
   ``string`` value::

ところで、以下は不正なコード例です。これは :ctype:`string` の値を書き換えようと
しているからです。

.. code-block:: cpp

    s[0] = 'x';
    (*p)[1] = 'y';

..
   In C++ terms, Go strings are a bit like ``const strings``, while pointers to
   strings are analogous to ``const string`` references.

C++ の言い方で言えば、 Go の文字列は :ctype:`const strings` と言えます。また、
これを参照するポインタも同じように :ctype:`const strings` への参照と言えます。

..
   Yes, there are pointers. However, Go simplifies their use a little; read on.

そう、ポインタがあります。でも Go のポインタは少し使いやすく簡単になって
います。見ていきましょう。

..
   Arrays are declared like this::

配列は以下のように宣言されます。

.. code-block:: cpp

    var arrayOfInt [10]int;

..
   Arrays, like strings, are values, but they are mutable. This differs from C,
   in which ``arrayOfInt`` would be usable as a pointer to ``int``. In Go,
   since arrays are values, it's meaningful (and useful) to talk about
   pointers to arrays.

配列は文字列のような、値の集まりですが、これらは変更可能です。 ``arrayOfInt``
と C との違いは、 :ctype:`int` へのポインタとして使う事が出来るところです。
Go では、配列は値の集まりで、配列へのポインタとして使えるという意味になります。

..
   The size of the array is part of its type; however, one can declare a slice variable, to which one can assign a pointer to any array with the same element type or -- much more commonly -- a slice expression of the form `a[low : high]`, representing the subarray indexed by `low` through `high-1`. Slices look a lot like arrays but have no explicit size (`[]` vs. `[10]`) and they reference a segment of an underlying, often anonymous, regular array. Multiple slices can share data if they represent pieces of the same array; multiple arrays can never share data.

この配列のサイズは型の一部なので、変数のスライスを宣言したり、 to which one can assign a pointer to any array with the same element type -- もっと一般的な利用例として -- スライスは ``a[low : high]`` と言う風に表現し、これによって元の配列の ``low`` から ``high-1`` の要素を持つ部分配列となります。スライスはほとんど配列のように見えますが、明確なサイズ情報を持たず (``[]`` vs. ``[10]``) 、they reference a segment of an underlying, often anonymous, regular array. 複数のスライスは、元の配列が同じものであればデータを共有することができますが、異なる複数の配列がデータを共有することは決してありません。

..
   Slices are much more common in Go programs than regular arrays; they're more flexible, have reference semantics, and are efficient. What they lack is the precise control of storage layout of a regular array; if you want to have a hundred elements of an array stored within your structure, you should use a regular array.

スライスは Go のプログラムでは正規の配列よりもずっと一般的で、フレキシブルで、参照の記法があり、効率的です。スライスに欠けているのは記憶域における正確なデータ構成の制御で、もしあなたが100個の配列要素を構造体の中に格納しようとするなら、正規の配列を使わなければいけません。

..
   When passing an array to a function, you almost always want to declare the formal parameter to be a slice. When you call the function, take the address of the array and Go will create (efficiently) a slice reference and pass that.

配列を関数に渡すとき、大体の場合、スライスを受け取るように宣言したいと思います。こうすれば関数を呼び出すとき、関数は配列のアドレスを受け取って、 Go はスライスの参照を作って(効率的に)渡すでしょう。

..
   Using slices one can write this function (from sum.go)::

..
    09    func sum(a []int) int {   // returns an int
    10        s := 0;
    11        for i := 0; i < len(a); i++ {
    12            s += a[i]
    13        }
    14        return s
    15    }

スライスの使い方として以下のように関数を書けます(sum.goより)。

.. code-block:: cpp
 
  09    func sum(a []int) int {   // intを返す
  10        s := 0;
  11        for i := 0; i < len(a); i++ {
  12            s += a[i]
  13        }
  14        return s
  15    }

..
   and invoke it like this::

..
   19        s := sum(&[3]int{1,2,3});  // a slice of the array is passed to sum

そして呼び出し側は以下のようになります。
 
.. code-block:: cpp

   19        s := sum(&[3]int{1,2,3});  // 配列のスライスをsumに渡す

..
   Note how the return type (int) is defined for sum() by stating it after the parameter list. The expression [3]int{1,2,3}?a type followed by a brace-bounded expression?is a constructor for a value, in this case an array of 3 ints. Putting an & in front gives us the address of a unique instance of the value. We pass the pointer to sum() by (implicitly) promoting it to a slice.

``sum()`` の返値の型 (:ctype:`int`) がパラメータリストの後ろに定義されていることに注意してください。 ``[3]int{1,2,3}`` という表現 -- 型の後ろにブレースに囲まれた表現がある -- は値のコンストラクタで、この例では3つの :ctype:`int` 値を持つ配列を作っています。 ``&`` を前に置くことで、値のインスタンスの唯一のアドレスを取得することが出来ます。 ``sum()`` 関数にポインタを渡すことで (暗黙的に) 配列をスライスに変形させています。

.. If you are creating a regular array but want the compiler to count the elements for you, use ... as the array size::

もし正規の配列を作るときにコンパイラに要素の数を数えさせるようにするには、 ... を配列のサイズとして使います。

.. code-block:: cpp

    s := sum(&[...]int{1,2,3});

.. In practice, though, unless you're meticulous about storage layout within a data structure, a slice itself?using empty brackets and no &?is all you need::

慣習として、もし記憶域でのデータ構成を気にしないのであれば、スライスをそのまま -- 空のブラケットに ``&`` 無しで -- 渡せば良いことになります。

.. code-block:: cpp

    s := sum([]int{1,2,3});

.. There are also maps, which you can initialize like this::

マップを使う場合は、以下のように初期化出来ます。

.. code-block:: cpp

    m := map[string]int{"one":1 , "two":2}

.. The built-in function len(), which returns number of elements, makes its first appearance in sum. It works on strings, arrays, slices, maps, and channels.

組み込み関数 ``len()`` は要素数を返しますが、最初にお見せした ``sum()`` 関数の中で使っています。これは文字列、配列、スライス、マップ、そしてチャンネルでも動作します。

.. An Interlude about Allocation[Top]

An Interlude about Allocation
=============================
.. Most types in Go are values. If you have an int or a struct or an array, assignment copies the contents of the object. To allocate a new variable, use new(), which returns a pointer to the allocated storage.

Goでは、ほとんどの型は値です。 :ctype:`int` や :ctype:`struct` や :ctype:`array` は代入時にオブジェクトの内容をコピーします。新しい変数を割り当てるためには ``new()`` を使います。 ``new()`` は割り当てた記憶域へのポインタを返します。

.. code-block:: cpp

    type T struct { a, b int }
    var t *T = new(T);

.. or the more idiomatic

またはより慣用的には次のようになります。

.. code-block:: cpp

    t := new(T);

.. Some types?maps, slices, and channels (see below)?have reference semantics. If you're holding a slice or a map and you modify its contents, other variables referencing the same underlying data will see the modification. For these three types you want to use the built-in function make():

マップやスライスやチャンネル(下記参照)のような型は参照セマンティックです。スライスやマップの内容を変更すると、同じデータを参照している他の変数でも変更が反映されます。これらの型を生成するには組み込み関数 ``make()`` を使います。

.. code-block:: cpp

    m := make(map[string]int);

.. This statement initializes a new map ready to store entries. If you just declare the map, as in

この文ではエントリーを格納する新しいマップを初期化しています。マップを宣言するためには次のようにします。

.. code-block:: cpp

    var m map[string]int;

.. it creates a nil reference that cannot hold anything. To use the map, you must first initialize the using make() or by assignment from an existing map.

ここではなにも保持していない ``nil`` 参照を生成しています。マップを使うためには、まずはじめに ``make()`` を使って参照を初期化するか既存のマップを代入する必要があります。

.. Note that new(T) returns type *T while make(T) returns type T. If you (mistakenly) allocate a reference object with new(), you receive a pointer to a nil reference, equivalent to declaring an uninitialized variable and taking its address.

``make(T)`` は ``T`` の型を返すのに対して ``new(T)`` は ``*T`` の型を返すことに注意してください。(間違えて) ``new()`` で参照オブジェクトの割り当てを行うとnil参照へのポインタが返されてしまいます。これは未初期化の変数を宣言してそのアドレスを受け取ることと同等です。

.. An Interlude about Constants[Top]

定数
==========
..
  Although integers come in lots of sizes in Go, integer constants do not.
  There are no constants like 0LL or 0x0UL. Instead, integer constants are
  evaluated as large-precision values that can overflow only when they are
  assigned to an integer variable with too little precision to represent the value.

Goでは多くの整数型サイズの変数がありますが、整数型定数はありません。
`0LL` や `0x0UL` のような定数はありません。
その代わり、単精度変数に割り当てようとして桁溢れした場合には
整数型定数は多精度変数として評価されます。

.. code-block:: cpp

    const hardEight = (1 << 100) >> 97  // これは正しい

..
  There are nuances that deserve redirection to the legalese of the language specification
  but here are some illustrative examples:

言語仕様には変換に関する記述がありますが、ここではいくつか実例を示します。

.. code-block:: cpp

    var a uint64 = 0  // uint64型 値0の変数
    a := uint64(0)    // "conversion"に相当する使い方です
    i := 0x1234       // iのデフォルト型はintとなります
    var j int = 1e6   // 正しい - 整数型では1000000に置き換えられます
    x := 1.5          // 浮動小数点型
    i3div2 := 3/2     // 整数型の除算 - 結果は1となります
    f3div2 := 3./2.   // 浮動小数点型の除算 - 結果は1.5となります

..
    var a uint64 = 0  // a has type uint64, value 0
    a := uint64(0)    // equivalent; uses a "conversion"
    i := 0x1234       // i gets default type: int
    var j int = 1e6   // legal - 1000000 is representable in an int
    x := 1.5          // a float
    i3div2 := 3/2     // integer division - result is 1
    f3div2 := 3./2.   // floating point division - result is 1.5

..
    Conversions only work for simple cases such as converting ints of
    one sign or size to another, and between ints and floats, plus
    a few other simple cases. There are no automatic numeric conversions of
    any kind in Go, other than that of making constants have concrete size
    and type when assigned to a variable.

型変換は、 `整数配列` の別シンボルへの変換や他のサイズとの変換のような簡単なケースや、
`整数配列` と `浮動小数点配列` の変換、 そしてその他のいくつかの簡単なケースでのみ動作します。
Goでは具体的なサイズと型を割り当てられていない変数はどんな型でも自動で変換はされません。

.. An I/O Package[Top]

I/O Package
=================

.. Next we'll look at a simple package for doing file I/O with the usual sort of open/close/read/write interface. Here's the start of file.go:

次に、ファイルのオープン、クローズ、読み込み、書き込みを行うインターフェイスを含んだシンプルなパッケージを見てみましょう。以下は\ ``file.go``\ の書き出しの部分です。

.. code-block:

   05    package file

   07    import (
   08        "os";
   09        "syscall";
   10    )

   12    type File struct {
.. 13        fd      int;    // file descriptor number
   13        fd      int;    // ファイル記述子番号
.. 14        name    string; // file name at Open time
   14        name    string; // ファイルを開く時の名前
   15    }

.. The first few lines declare the name of the package?file?and then import two packages. The os package hides the differences between various operating systems to give a consistent view of files and so on; here we're going to use its error handling utilities and reproduce the rudiments of its file I/O.

最初の数行でパッケージ名-ファイル名を宣言してから、2つのパッケージをインポートしています。\ :mod:`os`\ パッケージは様々なオペレーティングシステム間の違いを吸収して、ファイルなどを一貫して利用できる様にします。ここで、エラー制御ユーティリティを使用し、ファイルI/Oの基本を再生します。

.. The other item is the low-level, external syscall package, which provides a primitive interface to the underlying operating system's calls.

もう一方のパッケージは、基本的なオペレーティングシステムの呼び出しに原始的なインターフェイスを提供する、低レベルな外部\ :mod:`syscall`\ パッケージです。

.. Next is a type definition: the type keyword introduces a type declaration, in this case a data structure called File. To make things a little more interesting, our File includes the name of the file that the file descriptor refers to.

次は、型の定義です。\ ``File``\ というデータ構造を定義している様に、\ ``type``\ キーワードは型の宣言をする時に使用します。これの興味深い点は、この\ ``File``\ 型はファイル記述子が示すファイルの名前を含んでいるという点です。

Because File starts with a capital letter, the type is available outside the package, that is, by users of the package. In Go the rule about visibility of information is simple: if a name (of a top-level type, function, method, constant or variable, or of a structure field or method) is capitalized, users of the package may see it. Otherwise, the name and hence the thing being named is visible only inside the package in which it is declared. This is more than a convention; the rule is enforced by the compiler. In Go, the term for publicly visible names is ''exported''.

\ ``File``\ 型は、大文字から始まるため、型はパッケージの外部、つまり、パッケージを使用する側から見る事が出来ます。Go言語の情報可視性に関するルールは簡単です。もし(トップレベルの型、関数、メソッド、定数、変数、もしくは構造体のフィールド、メソッドの)名前が大文字で書かれている場合、パッケージを使用する側から見る事が出来ます。

In the case of File, all its fields are lower case and so invisible to users, but we will soon give it some exported, upper-case methods.

First, though, here is a factory to create a File:

.. code-block:: cpp

   17    func newFile(fd int, name string) *File {
   18        if fd < 0 {
   19            return nil
   20        }
   21        return &File{fd, name}
   22    }

This returns a pointer to a new File structure with the file descriptor and name filled in. This code uses Go's notion of a ''composite literal'', analogous to the ones used to build maps and arrays, to construct a new heap-allocated object. We could write

.. code-block:: cpp

      n := new(File);
      n.fd = fd;
      n.name = name;
      return n


but for simple structures like File it's easier to return the address of a nonce composite literal, as is done here on line 21.

We can use the factory to construct some familiar, exported variables of type *File:

24    var (
25        Stdin  = newFile(0, "/dev/stdin");
26        Stdout = newFile(1, "/dev/stdout");
27        Stderr = newFile(2, "/dev/stderr");
28    )
The newFile function was not exported because it's internal. The proper, exported factory to use is Open:

 
30    func Open(name string, mode int, perm int) (file *File, err os.Error) {
31        r, e := syscall.Open(name, mode, perm);
32        if e != 0 {
33            err = os.Errno(e);
34        }
35        return newFile(r, name), err
36    }
There are a number of new things in these few lines. First, Open returns multiple values, an File and an error (more about errors in a moment). We declare the multi-value return as a parenthesized list of declarations; syntactically they look just like a second parameter list. The function syscall.Open also has a multi-value return, which we can grab with the multi-variable declaration on line 31; it declares r and e to hold the two values, both of type int (although you'd have to look at the syscall package to see that). Finally, line 35 returns two values: a pointer to the new File and the error. If syscall.Open fails, the file descriptor r will be negative and NewFile will return nil.

About those errors: The os library includes a general notion of an error. It's a good idea to use its facility in your own interfaces, as we do here, for consistent error handling throughout Go code. In Open we use a conversion to translate Unix's integer errno value into the integer type os.Errno, which implements os.Error.

Now that we can build Files, we can write methods for them. To declare a method of a type, we define a function to have an explicit receiver of that type, placed in parentheses before the function name. Here are some methods for *File, each of which declares a receiver variable file.

 
38    func (file *File) Close() os.Error {
39        if file == nil {
40            return os.EINVAL
41        }
42        e := syscall.Close(file.fd);
43        file.fd = -1;  // so it can't be closed again
44        if e != 0 {
45            return os.Errno(e);
46        }
47        return nil
48    }

50    func (file *File) Read(b []byte) (ret int, err os.Error) {
51        if file == nil {
52            return -1, os.EINVAL
53        }
54        r, e := syscall.Read(file.fd, b);
55        if e != 0 {
56            err = os.Errno(e);
57        }
58        return int(r), err
59    }


61    func (file *File) Write(b []byte) (ret int, err os.Error) {
62        if file == nil {
63            return -1, os.EINVAL
64        }
65        r, e := syscall.Write(file.fd, b);
66        if e != 0 {
67            err = os.Errno(e);
68        }
69        return int(r), err
70    }


72    func (file *File) String() string {
73        return file.name
74    }

There is no implicit this and the receiver variable must be used to access members of the structure. Methods are not declared within the struct declaration itself. The struct declaration defines only data members. In fact, methods can be created for almost any type you name, such as an integer or array, not just for structs. We'll see an example with arrays later.

The String method is so called because of a printing convention we'll describe later.

The methods use the public variable os.EINVAL to return the (os.Error version of the) Unix error code EINVAL. The os library defines a standard set of such error values.

We can now use our new package:

 
05    package main

07    import (
08        "./file";
09        "fmt";
10        "os";
11    )


13    func main() {
14        hello := []byte{'h', 'e', 'l', 'l', 'o', ',', ' ', 'w', 'o', 'r', 'l', 'd', '\n'};
15        file.Stdout.Write(hello);
16        file, err := file.Open("/does/not/exist",  0,  0);
17        if file == nil {
18            fmt.Printf("can't open file; err=%s\n",  err.String());
19            os.Exit(1);
20        }
21    }

The ''./'' in the import of ''./file'' tells the compiler to use our own package rather than something from the directory of installed packages.

Finally we can run the program:

    % helloworld3
    hello, world
    can't open file; err=No such file or directory
    %

.. Rotting cats[Top]

Rotting cats
=================

.. Building on the file package, here's a simple version of the Unix utility cat(1), progs/cat.go:

fileパッケージで作成した簡易版のUnixコマンドのcat(1)が :file:`progs/cat.go` になります。

.. code-block:: cpp
   
   05    package main
   
   07    import (
   08        "./file";
   09        "flag";
   10        "fmt";
   11        "os";
   12    )
   
   14    func cat(f *file.File) {
   15        const NBUF = 512;
   16        var buf [NBUF]byte;
   17        for {
   18            switch nr, er := f.Read(&buf); true {
   19            case nr < 0:
   20                fmt.Fprintf(os.Stderr, "cat: error reading from %s: %s\n", f.String(), er.String());
   21                os.Exit(1);
   22            case nr == 0:  // EOF
   23                return;
   24            case nr > 0:
   25                if nw, ew := file.Stdout.Write(buf[0:nr]); nw != nr {
   26                    fmt.Fprintf(os.Stderr, "cat: error writing from %s: %s\n", f.String(), ew.String());
   27                }
   28            }
   29        }
   30    }
   
   32    func main() {
   33        flag.Parse();   // 引数のリストを読み取り、フラグをセットする
   34        if flag.NArg() == 0 {
   35            cat(file.Stdin);
   36        }
   37        for i := 0; i < flag.NArg(); i++ {
   38            f, err := file.Open(flag.Arg(i), 0, 0);
   39            if f == nil {
   40                fmt.Fprintf(os.Stderr, "cat: can't open %s: error %s\n", flag.Arg(i), err);
   41                os.Exit(1);
   42            }
   43            cat(f);
   44            f.Close();
   45        }
   46    }

By now this should be easy to follow, but the switch statement introduces some new features. Like a for loop, an if or switch can include an initialization statement. The switch on line 18 uses one to create variables nr and er to hold the return values from f.Read(). (The if on line 25 has the same idea.) The switch statement is general: it evaluates the cases from top to bottom looking for the first case that matches the value; the case expressions don't need to be constants or even integers, as long as they all have the same type.

Since the switch value is just true, we could leave it off?as is also the situation in a for statement, a missing value means true. In fact, such a switch is a form of if-else chain. While we're here, it should be mentioned that in switch statements each case has an implicit break.

Line 25 calls Write() by slicing the incoming buffer, which is itself a slice. Slices provide the standard Go way to handle I/O buffers.

Now let's make a variant of cat that optionally does rot13 on its input. It's easy to do by just processing the bytes, but instead we will exploit Go's notion of an interface.

.. The cat() subroutine uses only two methods of f: Read() and String(), so let's start by defining an interface that has exactly those two methods. Here is code from progs/cat_rot13.go:

cat()サブルーチンはfのRead()とString()という2つのメソッドのみしか使用しません。そこで、これら２つのメソッドを持つインタフェースを定義することから始めてみましょう。これは :file:`progs/cat_rot13.go` のコードです。

.. code-block:: cpp

   26    type reader interface {
   27        Read(b []byte) (ret int, err os.Error);
   28        String() string;
   29    }

Any type that has the two methods of reader?regardless of whatever other methods the type may also have?is said to implement the interface. Since file.File implements these methods, it implements the reader interface. We could tweak the cat subroutine to accept a reader instead of a *file.File and it would work just fine, but let's embellish a little first by writing a second type that implements reader, one that wraps an existing reader and does rot13 on the data. To do this, we just define the type and implement the methods and with no other bookkeeping, we have a second implementation of the reader interface.

.. code-block:: cpp
 
   31    type rotate13 struct {
   32        source    reader;
   33    }
   
   35    func newRotate13(source reader) *rotate13 {
   36        return &rotate13{source}
   37    }
   
   39    func (r13 *rotate13) Read(b []byte) (ret int, err os.Error) {
   40        r, e := r13.source.Read(b);
   41        for i := 0; i < r; i++ {
   42            b[i] = rot13(b[i])
   43        }
   44        return r, e
   45    }
   
   47    func (r13 *rotate13) String() string {
   48        return r13.source.String()
   49    }
   50    // end of rotate13 implementation

(The rot13 function called on line 42 is trivial and not worth reproducing here.)

To use the new feature, we define a flag:

.. code-block:: cpp
 
   14    var rot13Flag = flag.Bool("rot13", false, "rot13 the input")

and use it from within a mostly unchanged cat() function:

.. code-block:: cpp
 
   52    func cat(r reader) {
   53        const NBUF = 512;
   54        var buf [NBUF]byte;
   
   56        if *rot13Flag {
   57            r = newRotate13(r)
   58        }
   59        for {
   60            switch nr, er := r.Read(&buf); {
   61            case nr < 0:
   62                fmt.Fprintf(os.Stderr, "cat: error reading from %s: %s\n", r.String(), er.String());
   63                os.Exit(1);
   64            case nr == 0:  // EOF
   65                return;
   66            case nr > 0:
   67                nw, ew := file.Stdout.Write(buf[0:nr]);
   68                if nw != nr {
   69                    fmt.Fprintf(os.Stderr, "cat: error writing from %s: %s\n", r.String(), ew.String());
   70                }
   71            }
   72        }
   73    }

(We could also do the wrapping in main and leave cat() mostly alone, except for changing the type of the argument; consider that an exercise.) Lines 56 through 58 set it all up: If the rot13 flag is true, wrap the reader we received into a rotate13 and proceed. Note that the interface variables are values, not pointers: the argument is of type reader, not *reader, even though under the covers it holds a pointer to a struct.

Here it is in action:

.. code-block:: bash

   % echo abcdefghijklmnopqrstuvwxyz | ./cat
   abcdefghijklmnopqrstuvwxyz
   % echo abcdefghijklmnopqrstuvwxyz | ./cat --rot13
   nopqrstuvwxyzabcdefghijklm
   %

Fans of dependency injection may take cheer from how easily interfaces allow us to substitute the implementation of a file descriptor.

Interfaces are a distinctive feature of Go. An interface is implemented by a type if the type implements all the methods declared in the interface. This means that a type may implement an arbitrary number of different interfaces. There is no type hierarchy; things can be much more ad hoc, as we saw with rot13. The type file.File implements reader; it could also implement a writer, or any other interface built from its methods that fits the current situation. Consider the empty interface

    type Empty interface {}
Every type implements the empty interface, which makes it useful for things like containers.

.. Sorting
   =======

ソート
======

.. Interfaces provide a simple form of polymorphism. They completely separate the definition of what an object does from how it does it, allowing distinct implementations to be represented at different times by the same interface variable.

インターフェースはポリモルフィズムを簡単な形式で提供します。これはオブジェクトが行うことの定義といかにそれを行うかを分離し、同じインターフェース変数で時に応じて異なる実装を使わせることが可能となります。

.. As an example, consider this simple sort algorithm taken from progs/sort.go:

例として、progs/sort.goから取ってきた簡単なソートアルゴリズムを見てみましょう。

.. code-block:: cpp

   13    func Sort(data Interface) {
   14        for i := 1; i < data.Len(); i++ {
   15            for j := i; j > 0 && data.Less(j, j-1); j-- {
   16                data.Swap(j, j-1);
   17            }
   18        }
   19    }


.. The code needs only three methods, which we wrap into sort's Interface:

このコードは3つのメソッドを必要とします。これをソートのインターフェースにラップしてみましょう。

.. code-block:: cpp  

    07    type Interface interface {
    08        Len() int;
    09        Less(i, j int) bool;
    10        Swap(i, j int);
    11    }

.. We can apply Sort to any type that implements Len, Less, and Swap. The sort package includes the necessary methods to allow sorting of arrays of integers, strings, etc.; here's the code for arrays of int

Len, Less, Swapを実装したものであれば、どんな型でもSortを適用することが可能です。ソートパッケージは整数、文字列などの配列をソートするために必要となるメソッドを含んでいます。次に整数の配列をソートするコードを見てみましょう。

.. code-block:: cpp

   33    type IntArray []int
   
   35    func (p IntArray) Len() int            { return len(p); }
   36    func (p IntArray) Less(i, j int) bool  { return p[i] < p[j]; }
   37    func (p IntArray) Swap(i, j int)       { p[i], p[j] = p[j], p[i]; }

.. Here we see methods defined for non-struct types. You can define methods for any type you define and name in your package.

ここではnon-struct型のためのメソッド定義を見てきました。パッケージに定義したどんな型のメソッドも定義することが可能です。

.. And now a routine to test it out, from progs/sortmain.go. This uses a function in the sort package, omitted here for brevity, to test that the result is sorted.

progs/sortmain.gから、ここまでのコードをテストするルーチンを見てみます。

.. code-block:: cpp

   12    func ints() {
   13        data := []int{74, 59, 238, -784, 9845, 959, 905, 0, 0, 42, 7586, -5467984, 7586};
   14        a := sort.IntArray(data);
   15        sort.Sort(a);
   16        if !sort.IsSorted(a) {
   17            panic()
   18        }
   19    }

.. If we have a new type we want to be able to sort, all we need to do is to implement the three methods for that type, like this:

ある型をソートするためにしなければいけないことは次のように3つのメソッドを定義するだけです。

.. code-block:: cpp

   30    type day struct {
   31        num        int;
   32        shortName  string;
   33        longName   string;
   34    }

   36    type dayArray struct {
   37        data []*day;
   38    }

   40    func (p *dayArray) Len() int            { return len(p.data); }
   41    func (p *dayArray) Less(i, j int) bool  { return p.data[i].num < p.data[j].num; }
   42    func (p *dayArray) Swap(i, j int)       { p.data[i], p.data[j] = p.data[j], p.data[i]; }

Printing
=========

.. The examples of formatted printing so far have been modest. In this section we'll talk about how formatted I/O can be done well in Go.

これまでに挙げた出力フォーマットの例は、比較的単純なものでした。この章では、Goを用いてもう少し上手くI/Oを整形する方法を紹介します。

.. We've seen simple uses of the package fmt, which implements Printf, Fprintf, and so on. Within the fmt package, Printf is declared with this signature:

PrintfやFprintfなどが含まれるパッケージfmtについて簡単な使い方を見てきましたが、fmtパッケージにおいてPrintfは内部的に以下ように宣言されています:

.. code-block:: cpp

    Printf(format string, v ...) (n int, errno os.Error)

.. That ... represents the variadic argument list that in C would be handled using the stdarg.h macros but in Go is passed using an empty interface variable (interface {}) and then unpacked using the reflection library. It's off topic here but the use of reflection helps explain some of the nice properties of Go's Printf, due to the ability of Printf to discover the type of its arguments dynamically.

この3つのドット ... は可変長引数のリストを表しています。Cであればstdarg.hマクロを使って処理されるところですが、Goの場合は空のインターフェイス変数（interface {}）を通して、リフレクションライブラリによって展開されます。少しオフトピック気味ですが、GoのPrintfが持つすばらしい特性について説明するのにリフレクションはうってつけです。Printfは自身の引数の型を動的に見つけ出す事ができるのです。

.. For example, in C each format must correspond to the type of its argument. It's easier in many cases in Go. Instead of %llud you can just say %d; Printf knows the size and signedness of the integer and can do the right thing for you. The snippet

具体的には例えば 、Cでは各フォーマットがそれに対応する引数の型と完全に一致している必要があります。多くの場合Goはもっと簡単です。例えば%lludを指定する代わりに、%dとするだけでよいのです。Printfは整数型のサイズも符号の有無も知っており、あなたの代わりに常に正しい結果を導き出してくれるのです。スニペット:

.. code-block:: cpp

   10        var u64 uint64 = 1<<64-1;
   11        fmt.Printf("%d %d\n", u64, int64(u64));

これは以下のように出力されます::

 18446744073709551615 -1

.. In fact, if you're lazy the format %v will print, in a simple appropriate style, any value, even an array or structure. The output of

それでも面倒なら、%vを使えばどのような値でも（配列や構造体でも）、分かりやすく適切なかたちで出力されます。

.. code-block:: cpp

   14        type T struct { a int; b string };
   15        t := T{77, "Sunset Strip"};
   16        a := []int{1, 2, 3, 4};
   17        fmt.Printf("%v %v %v\n", u64, t, a);

.. is

これは以下のように出力されます::

  18446744073709551615 {77 Sunset Strip} [1 2 3 4]

.. You can drop the formatting altogether if you use Print or Println instead of Printf. Those routines do fully automatic formatting. The Print function just prints its elements out using the equivalent of %v while Println inserts spaces between arguments and adds a newline. The output of each of these two lines is identical to that of the Printf call above.

Printfの代わりにPrintやPrintlnを使えば、フォーマットは必要ありません。これらは自動的にフォーマット処理を行います。具体的には引数の要素に対し%vに相当する処理を行い、Printが結果をそのまま出力するのに対してPrintlnは各要素の間にスペースを追加し、末尾に改行を加えます。

.. code-block:: cpp

  18        fmt.Print(u64, " ", t, " ", a, "\n");
  19        fmt.Println(u64, t, a);

.. If you have your own type you'd like Printf or Print to format, just give it a String() method that returns a string. The print routines will examine the value to inquire whether it implements the method and if so, use it rather than some other formatting. Here's a simple example.

もしあなたが独自の型をPrintfやPrintにフォーマットさせたければ、string型の返り値を持つString()メソッドを用意しておくだけでよいのです。printのルーティンはフォーマットする値にメソッドが実装されているかどうかを検査し、もしそうであれば他のどのフォーマット処理でもなくそのメソッドを使います。わかりやすい例を示します。

.. code-block:: cpp
 
  09    type testType struct { a int; b string }
  
  11    func (t *testType) String() string {
  12        return fmt.Sprint(t.a) + " " + t.b
  13    }
     
     
  15    func main() {
  16        t := &testType{77, "Sunset Strip"};
  17        fmt.Println(t)
  18    }

.. Since *testType has a String() method, the default formatter for that type will use it and produce the output

``*testType`` はString()メソッドを持っているので、その型のデフォルトフォーマッタはこのメソッドを使って出力を行うことになります。

.. code-block:: cpp

   77 Sunset Strip

.. Observe that the String() method calls Sprint (the obvious Go variant that returns a string) to do its formatting; special formatters can use the fmt library recursively.

String()メソッドがそのフォーマットを行う為にSprint（stringを返す明らかなGoの異形です）をコールしていることに注目してください。特別なフォーマッタはfmtライブラリを再帰的に使うことが出来ます。

.. Another feature of Printf is that the format %T will print a string representation of the type of a value, which can be handy when debugging polymorphic code.

Printfがもつその他の機能としては、対象の値の型を出力する%T指定子があります。これはポリモーフィックなコードをデバッグする際に重宝します。

.. It's possible to write full custom print formats with flags and precisions and such, but that's getting a little off the main thread so we'll leave it as an exploration exercise.

フラグや精度を用いて完全なカスタムフォーマットを指定することも可能ですが、本題から少しそれるので、探求する余地を残しておくつもりです。

.. You might ask, though, how Printf can tell whether a type implements the String() method. Actually what it does is ask if the value can be converted to an interface variable that implements the method. Schematically, given a value v, it does this:

もっとも、あなたはString()が実装している型をPrintfがどうやって判断しているのかをを知りたいかもしれません。 実際には、対象となる値がメソッドを実装するインターフェイス変数に変換できるかどうかを調べています。概念的には、与えられた値vに対して以下のような処理がなされます:

.. code-block:: cpp

    type Stringer interface {
        String() string
    }
    s, ok := v.(Stringer);  // Test whether v implements "String()"
    if ok {
        result = s.String()
    } else {
        result = defaultOutput(v)
    }

.. The code uses a ``type assertion'' (v.(Stringer)) to test if the value stored in v satisfies the Stringer interface; if it does, s will become an interface variable implementing the method and ok will be true. We then use the interface variable to call the method. (The ''comma, ok'' pattern is a Go idiom used to test the success of operations such as type conversion, map update, communications, and so on, although this is the only appearance in this tutorial.) If the value does not satisfy the interface, ok will be false.

このコードはvに格納された値がStringerインターフェイスの要件を満たすかどうかをテストするために``type assertion'' （v.(Stringer)）を使っています。要件を満たす場合、sはメソッドを実装したインターフェイス変数となり、okはtrueとなります。（この”カンマ, ok”というパターンはGoにおけるイディオムのひとつで、型の変換・マップアップデート・コミュニケーション等によく使われますが、このチュートリアルではここでしか登場しません。）反対に要件が満たされない場合、okはfalseとなります。

.. In this snippet the name Stringer follows the convention that we add ''[e]r'' to interfaces describing simple method sets like this.

このスニペットにおけるStringerという名前は、このようにシンプルなメソッドセットを表現するインターフェイスに"[e]r"を付加するという慣習に倣っています。

.. One last wrinkle. To complete the suite, besides Printf etc. and Sprintf etc., there are also Fprintf etc. Unlike in C, Fprintf's first argument is not a file. Instead, it is a variable of type io.Writer, which is an interface type defined in the io library:

最後にもう一つ助言を。PrintfやSprintfの他にも、Fprintfなどがあります。但しCとは違い、第一引数はファイルではありません。代わりにio.Wirter型の変数をとります。それはioライブラリ内で定義されているインターフェイス型です:

.. code-block:: cpp

    type Writer interface {
        Write(p []byte) (n int, err os.Error);
    }

.. (This interface is another conventional name, this time for Write; there are also io.Reader, io.ReadWriter, and so on.) Thus you can call Fprintf on any type that implements a standard Write() method, not just files but also network channels, buffers, whatever you want.

（このインターフェイスはもう一つの慣習的な命名規則を用いています。ここではWiteですが、他にもio.Readerやio.ReadWriter等があります。）この事によって、たとえそれがどんな型であっても標準的なWrite()メソッドを実装していさえすれば、ファイルのみにとどまらずネットワークチャンネルやバッファ等からFprintfをコールする事が出来ます。

.. Prime numbers

素数の計算
==========

.. Now we come to processes and communication?concurrent programming. It's a big subject so to be brief we assume some familiarity with the topic.

それでは、並列プログラミングのプロセスとコミュニケーションの話しを始めたいと思います。この話題はとても大きな話題なので、ある程度、並列プログラミングについて知っているという仮定で手短に説明してきます。

.. A classic program in the style is a prime sieve. (The sieve of Eratosthenes is computationally more efficient than the algorithm presented here, but we are more interested in concurrency than algorithmics at the moment.) It works by taking a stream of all the natural numbers and introducing a sequence of filters, one for each prime, to winnow the multiples of that prime. At each step we have a sequence of filters of the primes so far, and the next number to pop out is the next prime, which triggers the creation of the next filter in the chain.

古典的な素数計算のプログラムは、素数のふるいとして実装されます。計算上はここで説明するものよりも、エラトステネスのふるいの方が効率がいいのですが、ここではアルゴリズムよりも並列計算にフォーカスしています。これは、すべての自然数を含むストリームを受け取り、それぞれの素数ごとに、その倍数を排除していくフィルタが列状に連なっています。それぞれのステップでは、それまでに計算された素数のフィルタの列がある状態から始まり、そのフィルタを通り抜けた次の数が、次の素数ということになります。その素数によって、次のフィルタが作成されます。

.. Here's a flow diagram; each box represents a filter element whose creation is triggered by the first number that flowed from the elements before it.

以下の画像はフローを表した図になります。それぞれの箱はフィルタを表し、その前のフィルタから出てきた最初の数値を使って作られます。

.. Image:: sieve.gif

.. To create a stream of integers, we use a Go channel, which, borrowing from CSP's descendants, represents a communications channel that can connect two concurrent computations. In Go, channel variables are references to a run-time object that coordinates the communication; as with maps and slices, use make to create a new channel.

整数のストリームを作成するには、Goのチャンネルを使用します。これはCSP(Communicating Sequential Processes?)の子孫から借りてきた機能で、２つの並列計算プログラムをつなぐ、通信チャンネルとして表現されます。Goでは、チャンネル変数はコミュニケーションの面倒を見る実行時オブジェクトへの参照になっています。マップやスライスを使用するのと同じように、使用すると、新しいチャンネルを作成します。

.. Here is the first function in progs/sieve.go:

以下のコードは、 :file:`progs/sieve.go` の最初の関数です。


.. code-block:: cpp 

   09    // チャンネル'ch'に対して、2, 3, 4, ... という数値の列を順番に送信します
   10    func generate(ch chan int) {
   11        for i := 2; ; i++ {
   12            ch <- i  // 'i'をチャンネル'ch'に送信
   13        }
   14    }

.. 09    // Send the sequence 2, 3, 4, ... to channel 'ch'.
   12            ch <- i  // Send 'i' to channel 'ch'.

.. The generate function sends the sequence 2, 3, 4, 5, ... to its argument channel, ch, using the binary communications operator <-. Channel operations block, so if there's no recipient for the value on ch, the send operation will wait until one becomes available.

この生成関数は、 2, 3, 4, 5という数値の列を、引数で渡されたチャンネル'ch'に送信します。送信するときは、バイナリ通信演算子の ``<-`` を使用します。チャンネルの操作を行うとブロックします。そのため、もしも'ch'の値を受け取るコードがなければ、次にチャンネルが操作可能になるまで送信操作は待たされることになります。

.. The filter function has three arguments: an input channel, an output channel, and a prime number. It copies values from the input to the output, discarding anything divisible by the prime. The unary communications operator <- (receive) retrieves the next value on the channel.

Filter関数は3つの引数を持っています。入力のチャンネルと、出力のチャンネル、および素数になります。この関数は入力のチャンネルの値を受け取って、値のコピーを出力のチャンネルに送信しますが、渡された素数で割ることが可能な値が来た場合は途中で破棄します。単項の通信演算子の ``<-`` を使って、入力のチャンネルの次の値を受信します。

.. code-block:: cpp

  16    // 'in'チャンネルから値をコピーして、'out'チャンネルに送信します
  17    // 'prime'で割ることが可能な数は削除します
  18    func filter(in, out chan int, prime int) {
  19        for {
  20            i := <-in;  // 'in'から値を受信して、'i'に格納
  21            if i % prime != 0 {
  22                out <- i  // 'i'を'out'チャンネルに送信
  23            }
  24        }
  25    }

.. 16    // Copy the values from channel 'in' to channel 'out',
   17    // removing those divisible by 'prime'.
   20            i := <-in;  // Receive value of new variable 'i' from 'in'.
   22                out <- i  // Send 'i' to channel 'out'.

.. The generator and filters execute concurrently. Go has its own model of process/threads/light-weight processes/coroutines, so to avoid notational confusion we call concurrently executing computations in Go goroutines. To start a goroutine, invoke the function, prefixing the call with the keyword go; this starts the function running in parallel with the current computation but in the same address space:

ジェネレータとフィルターはそれぞれ並列に実行されます。Goはプロセス/スレッド/軽量プロセス/コルーチンにあたる固有の機能を持っています。表記上の混乱を避けるために、Goではこのような並列計算実行をGoroutineと呼んでいます。関数を呼び出してgoroutineを開始するためには、 ``go`` というキーワードを前につけて呼びます。こうすると、同じアドレス空間で並列に関数を実行することができます。

.. code-block:: cpp

    go sum(hugeArray); // バックグラウンドで合計を計算します

..  go sum(hugeArray); // calculate sum in the background

.. If you want to know when the calculation is done, pass a channel on which it can report back:

もしも計算が完了したのを知りたければ、チャンネルを渡して結果を返してもらうようにします:

.. code-block:: cpp

    ch := make(chan int);
    go sum(hugeArray, ch);
    // ... しばらくの間別のことをします
    result := <-ch;  // 並列で計算している関数の終了を待って、結果を受け取ります

..  // ... do something else for a while
    result := <-ch;  // wait for, and retrieve, result

.. Back to our prime sieve. Here's how the sieve pipeline is stitched together:

それでは、サンプルの、素数のふるいの例に戻ります。ここでは、ふるいのパイプラインをつなぎ合わせています:

.. code-block:: cpp

  28    func main() {
  29        ch := make(chan int);  // 新しいチャンネルを作成します
  30        go generate(ch);  // goroutineとしてgenerate()を実行します
  31        for {
  32            prime := <-ch;
  33            fmt.Println(prime);
  34            ch1 := make(chan int);
  35            go filter(ch, ch1, prime);
  36            ch = ch1
  37        }
  38    }

.. 29        ch := make(chan int);  // Create a new channel.
   30        go generate(ch);  // Start generate() as a goroutine.

.. Line 29 creates the initial channel to pass to generate, which it then starts up. As each prime pops out of the channel, a new filter is added to the pipeline and its output becomes the new value of ch.

29行目ではチャンネルを初期化してgenerate()関数に渡しています。generate()はチャンネルに値を入れ始めます。それぞれの素数はチャンネルから出力されます。出力されると、新しいフィルタがパイプラインに追加され、そのフィルタの出力チャンネルが、新しい'ch'の値になります。

.. The sieve program can be tweaked to use a pattern common in this style of programming. Here is a variant version of generate, from progs/sieve1.go:

ふるいのプログラムを変更して、このスタイルのプログラムの一般的なパターンを使用してみます。以下のプログラムはgenerate()関数の別バージョンです。これは :file:`progs/sieve1.go` に格納されています:

.. code-block:: cpp
 
  10    func generate() chan int {
  11        ch := make(chan int);
  12        go func(){
  13            for i := 2; ; i++ {
  14                ch <- i
  15            }
  16        }();
  17        return ch;
  18    }

.. This version does all the setup internally. It creates the output channel, launches a goroutine running a function literal, and returns the channel to the caller. It is a factory for concurrent execution, starting the goroutine and returning its connection.

このバージョンはすべてのセットアップを内部で行っています。内部で出力チャンネルを作成し、関数リテラルをgoroutineとして実行しています。最後に、呼び出し元に、内部で作成したチャンネルを返しています。これは並列実行のためのファクトリ関数になっていて、goroutineを実行してコネクションを返すようになっています。

.. The function literal notation (lines 12-16) allows us to construct an anonymous function and invoke it on the spot. Notice that the local variable ch is available to the function literal and lives on even after generate returns.

関数のリテラル表記(12行目-16行目)を使うと、無名関数を作ることができて、その場で実行することができます。ローカル変数の ``ch`` は関数リテラルの中でも使用することができ、generate()関数から抜けた後も使用することができます。

.. The same change can be made to filter:

同じ変更をfilter()関数にも適用します:

.. code-block:: cpp
 
  21    func filter(in chan int, prime int) chan int {
  22        out := make(chan int);
  23        go func() {
  24            for {
  25                if i := <-in; i % prime != 0 {
  26                    out <- i
  27                }
  28            }
  29        }();
  30        return out;
  31    }

.. The sieve function's main loop becomes simpler and clearer as a result, and while we're at it let's turn it into a factory too:

sieve(ふるい)関数のメインループは、呼ばれる側の関数をファクトリに変更したために、こちらもシンプルでクリーンになりました:
 
.. code-block:: cpp

   33    func sieve() chan int {
   34        out := make(chan int);
   35        go func() {
   36            ch := generate();
   37            for {
   38                prime := <-ch;
   39                out <- prime;
   40                ch = filter(ch, prime);
   41            }
   42        }();
   43        return out;
   44    }

.. Now main's interface to the prime sieve is a channel of primes:

素数のふるいを行うmainのインタフェースは、primesチャンネルになりました:

.. code-block:: cpp 

   46    func main() {
   47        primes := sieve();
   48        for {
   49            fmt.Println(<-primes);
   50        }
   51    }

.. Multiplexing
   ============

多重化
======

.. With channels, it's possible to serve multiple independent client goroutines without writing an explicit multiplexer. The trick is to send the server a channel in the message, which it will then use to reply to the original sender. A realistic client-server program is a lot of code, so here is a very simple substitute to illustrate the idea. It starts by defining a request type, which embeds a channel that will be used for the reply.

.. FIXME:
channelを使うことによって複数の独立したgoroutineをmultiplexerを書くことなく処理することが出来ます。channelをメッセージに含めてサーバーに送信し、それを使って送信元に返事をします。現実的なクライアントサーバープログラムはコード量が多いので、ここでは簡略化したものを使って説明を行います。これはリクエスト型の定義から始まり、その中には返事するために使用するchannelが組込まれています。

.. code-block:: cpp

    09    type request struct {
    10        a, b    int;
    11        replyc  chan int;
    12    }

.. The server will be trivial: it will do simple binary operations on integers. Here's the code that invokes the operation and responds to the request:

サーバーは簡単なもので、整数のバイナリ操作を行います。ここで処理をしてリクエストに返事を返すコードを見ていきます。

.. code-block:: cpp 

    14    type binOp func(a, b int) int
    
    16    func run(op binOp, req *request) {
    17        reply := op(req.a, req.b);
    18        req.replyc <- reply;
    19    }

.. Line 18 defines the name binOp to be a function taking two integers and returning a third.

18行目でbinOpを整数値を2つ取り、3つ目のものを返す関数として定義しています。

.. The server routine loops forever, receiving requests and, to avoid blocking due to a long-running operation, starting a goroutine to do the actual work.

サーバールーチンは延々とループし続け、リクエストを受けとり、処理をブロックさせないようにgoroutineを開始して実際の処理をさせます。

.. code-block:: cpp

    21    func server(op binOp, service chan *request) {
    22        for {
    23            req := <-service;
    24            go run(op, req);  // don't wait for it
    25        }
    26    }

.. We construct a server in a familiar way, starting it and returning a channel connected to it:

サーバーを見慣れた方法で組み立てます。サーバーを開始してそれに接続したchannelを返します。

.. code-block:: cpp

    28    func startServer(op binOp) chan *request {
    29        req := make(chan *request);
    30        go server(op, req);
    31        return req;
    32    }

.. Here's a simple test. It starts a server with an addition operator and sends out N requests without waiting for the replies. Only after all the requests are sent does it check the results.

次に簡単なテストです。これはサーバーをオペレーターを付加して開始し、Nリクエストを返事を待たずに送信します。すべてのリクエストの送信が終わった時点で結果のチェックを行います。

.. code-block:: cpp

    34    func main() {
    35        adder := startServer(func(a, b int) int { return a + b });
    36        const N = 100;
    37        var reqs [N]request;
    38        for i := 0; i < N; i++ {
    39            req := &reqs[i];
    40            req.a = i;
    41            req.b = i + N;
    42            req.replyc = make(chan int);
    43            adder <- req;
    44        }
    45        for i := N-1; i >= 0; i-- {   // doesn't matter what order
    46            if <-reqs[i].replyc != N + 2*i {
    47                fmt.Println("fail at", i);
    48            }
    49        }
    50        fmt.Println("done");
    51    }

.. One annoyance with this program is that it doesn't shut down the server cleanly; when main returns there are a number of lingering goroutines blocked on communication. To solve this, we can provide a second, quit channel to the server:

このプログラムの厄介なところはサーバーがきれいにシャットダウンされないことです。mainが返る時にいくつかのgoroutineが通信中のままブロックされて残ってしまいます。これを解決するためにquit channelをサーバーに渡します。

.. code-block:: cpp

    32    func startServer(op binOp) (service chan *request, quit chan bool) {
    33        service = make(chan *request);
    34        quit = make(chan bool);
    35        go server(op, service, quit);
    36        return service, quit;
    37    }

.. It passes the quit channel to the server function, which uses it like this:

quit channelをサーバー関数に渡し、サーバーはそれを次のようにして使います。

.. code-block:: cpp

    21    func server(op binOp, service chan *request, quit chan bool) {
    22        for {
    23            select {
    24            case req := <-service:
    25                go run(op, req);  // don't wait for it
    26            case <-quit:
    27                return;
    28            }
    29        }
    30    }

.. Inside server, the select statement chooses which of the multiple communications listed by its cases can proceed. If all are blocked, it waits until one can proceed; if multiple can proceed, it chooses one at random. In this instance, the select allows the server to honor requests until it receives a quit message, at which point it returns, terminating its execution.

サーバー内でselect文はcaseで並んでいる複数の通信のうち開始出来るものを選択します。もしすべてブロックされていれば、そのうちの1つが開始出来る状態になるまで待ちます。複数のものが開始出来る状態となれば、ランダムでそのうちの1つが選択されます。この例では、selectを使うことでquitメッセージを受けとるまでサーバーにリクエストを待たせ、受け取った時点で実行を終了させることが出来ます。

.. All that's left is to strobe the quit channel at the end of main:

.. FIXME
あとはmainの終わりにあるquit channelをstrobeするだけです。

.. code-block:: cpp

    40        adder, quit := startServer(func(a, b int) int { return a + b });

...

.. code-block:: cpp

    55        quit <- true;

.. There's a lot more to Go programming and concurrent programming in general but this quick tour should give you some of the basics.

Goプログラミングや一般的な並列処理プログラミングはこれだけではありませんが、基礎的なところはは理解いただけたでしょう。


Except as noted, this content is licensed under Creative Commons Attribution 3.0.
