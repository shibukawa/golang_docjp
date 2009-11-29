===
FAQ
===

.. Origins
   =======

Goの起源
========

.. **What is the purpose of the project?**

このプロジェクトの目的は何ですか？
----------------------------------

.. No major systems language has emerged in over a decade, but over that time the computing landscape has changed tremendously. There are several trends:

ここ10年以上の間、メジャーなシステム言語と呼ばれるものは登場していません。しかし、この間コンピュータをめぐる景色は大きく変化しています。この変化には以下のような傾向があります:

.. * Computers are enormously quicker but software development is not faster.
   * Dependency management is a big part of software development today but the “header files” of languages in the C tradition are antithetical to clean dependency analysis—and fast compilation.
   * There is a growing rebellion against cumbersome type systems like those of Java and C++, pushing people towards dynamically typed languages such as Python and JavaScript.
   * Some fundamental concepts such as garbage collection and parallel computation are not well supported by popular systems languages.
   * The emergence of multicore computers has generated worry and confusion.

* コンピュータはとてつもなく速くなりましたがが、ソフトウェア開発は速くなっていません。
* 依存関係の管理というものが、今日のソフトウェア開発において、大きな位置を占めていますが、C言語の伝統のヘッダファイルはクリーンな依存性分析と高速なコンパイルとは対極です。
* JavaやC++のような扱いにくい型システムに対する反乱が徐々に拡大しつつあります。PythonやJavaScriptのような、動的な型を持つ言語に対して、人々が押し寄せてきています。
* 人気のあるシステム言語の中には、ガーベジコレクションや並列計算のような基本概念をサポートしていないものがあります。
* マルチコアのコンピュータの出現によって、人々の間に、不安と混乱が発生しました。

.. We believe it's worth trying again with a new language, a concurrent, garbage-collected language with fast compilation. Regarding the points above:

私たちは、並列をサポートし、ガベージコレクションを備えた、コンパイルの速い言語に挑戦することは価値があることだと信じています。上記の点に関して:

.. * It is possible to compile a large Go program in a few seconds on a single computer.
   * Go provides a model for software construction that makes dependency analysis easy and avoids much of the overhead of C-style include files and libraries.
   * Go's type system has no hierarchy, so no time is spent defining the relationships between types. Also, although Go has static types the language attempts to make types feel lighter weight than in typical OO languages.
   * Go is fully garbage-collected and provides fundamental support for concurrent execution and communication.
   * By its design, Go proposes an approach for the construction of system software on multicore machines.

* 大きなGoのプログラムを一台のコンピュータで数秒で数秒以内にコンパイルすることが可能です。
* Goは、依存性の分析を簡単にし、C言語スタイルのインクルードファイルや、ライブラリの持つオーバーヘッドのほとんどを解消するようなソフトウェアの構成が行えるモデルを提供します。
* Goの型システムには階層構造がないため、型同士の関係を定義するに貴重な時間を使う必要はありません。また、Goは静的な型システムを持つ言語ですが、典型的なオブジェクト指向言語と比べると型を作成するのが気軽に行えるような言語になっています。
* Goは完全なガーベジコレクションを備え、言語レベルで並列実行とコミュニケーションの機能をサポートしています。
* これらのデザインにより、Goはマルチコアのマシン上で動作する、システムソフトウェアの構築を行える様々な機能を提供しています。

.. **What is the origin of the name?**

名前の由来は？
--------------

.. “Ogle” would be a good name for a Go debugger.

"Ogle"というのはGoのデバッガーの名前としていい名前になると思います。

.. **What kind of a name is 6g?**

6gというのは何を指す名前ですか？
--------------------------------

.. The 6g (and 8g and 5g) compiler is named in the tradition of the Plan 9 C compilers, described in http://plan9.bell-labs.com/sys/doc/compiler.html (see the table in section 2). 6 is the architecture letter for amd64 (or x86-64, if you prefer), while g stands for Go.

6g(と、8g, 5g)というのは伝統的なPlan 9のC言語のコンパイラの名前です。詳しい説明は http://plan9.bell-labs.com/sys/doc/compiler.html のSection 2の表を参照してください。6というのはアーキテクチャを表す文字で、AMD64(あるいはx86-64という呼び方が好きな人もいるでしょう)を表します。gはGoを表しています。

.. **Why not just write some libraries for C++ to do communication?**

C++のライブラリとコミュニケーションを行うために、なぜライブラリを書く必要があるのですか？
-----------------------------------------------------------------------------------------

.. We considered doing that, but too many of the problems—lack of garbage collection, long dependency chains, nested include files, lack of concurrency awareness—are rooted in the design of the C and C++ languages themselves. We felt a viable solution required a more complete approach.

私たちもこのことについて考えました。ですが、ガーベジコレクションが無い、依存性のチェーンが異様に長い、ネストされたインクルード、並列実行のサポートがないなど、C/C++の言語設計そのものに起因する多くの問題があります。C/C++をサポートするには、もっと根本からアプローチしていかなければ実現可能な解は得られないだろう、と考えています。

.. **Why doesn't Go run on Windows?**

なぜGoはWindows上で実行できないのですか？
-----------------------------------------

.. We understand that a significant fraction of computers in the world run Windows and it would be great if those computers could run Go programs. However, the Go team is small and we don't have the resources to do a Windows port at the moment. We would be more than willing to answer questions and offer advice to anyone willing to develop a Windows version.

私たちも、世界中のコンピュータのうちのWindowsが走っているものが大多数を占めていて、それらのコンピュータの上でGoのプログラムが動けばどれだけすばらしいことだろう、と思います。しかし、Goのチームは小さいために、Windowsに今すぐ移植を行うリソースがありません。ただ、もしもWindows版の開発をしたいと考えている人がいるのであれば、質問に答えたりアドバイスをしたりというのは積極的に行っていきたいと思っています。

.. note::

   訳注) Windowsに限らず、 `様々な環境への移植 <http://go-lang.cat-v.org/os-ports>`_ が行われています。

.. Usage

扱い方
=======

.. **Who should use the language?**

だれが言語を使うのですか？
---------------------------

.. Go is an experiment. We hope adventurous users will give it a try and see if they enjoy it.
.. Not every programmer will, but we hope enough will find satisfaction in the approach it offers to justify further development.

Go は、実験中です。 我々は、冒険心があり、いろいろと試し楽しめる人を望んでいます。
すべてのプログラマではないです、しかし我々は十分にさらなる発展を正当化するため提供しているアプローチで満足感を得ることを望んでいます。

.. Is Google using Go internally?

Google内部でGoを使うのですか？
--------------------------------

.. The Go project was conceived to make it easier to write the kind of servers and other software Google uses internally, but the implementation isn't quite mature enough yet for large-scale production use.
.. While we continue development we are also doing experiments with the language as a candidate server environment.
.. It's getting there.
.. For instance, the server behind http://golang.org is a Go program; in fact it's just the godoc document server running in a production configuration.

Go プロジェクトでは、Google 内部で、簡単にいろいろな種類のサーバーや他のGoogle ソフトウェアを記述するように考案されています。
しかし、まだ大規模なプロダクトで使用するために、十分に成熟してはいません。
一方で、我々はまたサーバ環境としての候補として、Goを使って、実験を続け開発をしています。
もう一息というところです。
例えば、http://golang.org/ の背後にあるサーバーは、Goでできています。
それはちょうどgodocドキュメントサーバーが動作しているという事実です。

.. Do Go programs link with C/C++ programs?

Go は、C/C++プログラムとリンクできますか？
----------------------------------------------

.. There are two Go compiler implementations, 6g and friends, generically called gc, and gccgo.
.. Gc uses a different calling convention and linker and can therefore only be linked with C programs using the same convention.  There is such a C compiler but no C++ compiler.
.. Gccgo is a GCC front-end that can, with care, be linked with GCC-compiled C or C++ programs. However, because Go is garbage-collected it will be unwise to do so, at least naively.

2つのGoコンパイラの実装では、"6g"他がり、一般的にGCかgccgoが呼ばれています。
GCを使用すると、別の呼び出し規約とリンカのため、唯一のCプログラムとリンクすることができます。
これは、Cコンパイラであって、C++コンパイラではありません。
Gccgo は、GCCのフロントエンドとして使うことができ、GCCでコンパイルされた、C/C++プログラムとリンクすることができます。
Go は、ガベージコレクションをするため、単純にリンクさせることは、賢明ではありません。

.. There is a “foreign function interface” to allow safe calling of C-written libraries from Go code.
.. We expect to use SWIG to extend this capability to C++ libraries. There is no safe way to call Go code from C or C++ yet.

"外部関数インターフェイス"は、Goのコードから安全に、"C"で書かれたライブラリを呼び出すことができます。
我々は、SWIGを使って、C++ライブラリが利用できるようになることを期待しています。
C/C++からGoコードを安全に呼び出す方法はまだありません。

.. Does Go support Google's protocol buffers?

Go は、Googleの"protocol buffers"をサポートしますか？
--------------------------------------------------------

.. Protocol buffers are supported.
.. We plan to have the next release of the protocol buffer source code include Go code generators and a Go library for them. 
.. The implementation uses data reflection at run time so it is slow, but a new implementation is planned.

Protocol buffers は、サポートします。
我々の計画では、次のリリースに、"protocol buffer"のライブラリとジェネレータを取り込みます。
データリフレクションを使っていますので処理時間は遅いです。しかし、新しい実装を計画しています。


.. Design

設計
====

.. Why doesn't Go have feature X?

なぜGoにはXという機能がないのですか？
-------------------------------------

.. Every language contains novel features and omits someone's favorite feature. Go was designed with an eye on felicity of programming, speed of compilation, orthogonality of concepts, and the need to support features such as concurrency and garbage collection. Your favorite feature may be missing because it doesn't fit, because it affects compilation speed or clarity of design, or because it would make the fundamental system model too difficult.

どんな言語であっても、目新しい機能が含まれている反面、誰かが好きな機能を省略することがありえます。Goはプログラミングの喜び、コンパイル速度、概念の直交性、並列実行やガーベジコレクション機能のサポートに重点をおいてデザインされています。みなさんの好きな機能がないとすれば、それはコンパイル速度や明確な設計などとマッチしないか、基本的なシステムのモデルを複雑にしてしまうから、という理由からだと思います。

.. If it bothers you that Go is missing feature X, please forgive us and investigate the features that Go does have. You might find that they compensate in interesting ways for the lack of X.

もしもGoに機能Xがないために苦労を強いられているのであれば、私たちに恨みを向けないで、Goが持っている機能を調べてみてください。もしかしたらXの機能が欠けている部分が、面白い方法で補完されているの、というのを見つけることができるかもしれません。

.. Why is the syntax so different from C++?

なぜ文法はC++と異なっているのですか？
-------------------------------------

.. This and other language design questions are answered in the separate language design FAQ.

この質問や、言語設計に関するその他の質問に関しては、 :ref:`language_design_faq` にて回答します。

.. Object-Oriented Programming

オブジェクト指向プログラミング
==============================

.. Is Go an object-oriented language?

Goはオブジェクト指向言語ですか？
------------------------------------

.. Yes and no. Although Go has types and methods and allows an object-oriented style of programming, there is no type hierarchy. The concept of “interface” in Go provides a different approach that we believe is easy to use and in some ways more general. There are also ways to embed types in other types to provide something analogous—but not identical—to subclassing. Moreover, methods in Go are more general than in C++ or Java: they can be defined for any sort of data, not just structs.

イエスとも言えますし、ノーとも言えます。Goは型もメソッドもあり、オブジェクト指向のスタイルのプログラミングを行うことができますが、型には階層はありません。Goでは"インタフェース"という概念が提供されていて、今までとは異なるアプローチを行うことができます。私たちは簡単に使えると信じていますし、いくつかの方法の中ではより汎用的だと思っています。また、他の型に対して、型を埋め込んで類似のモノを提供するということもできますが、サブクラスとは異なります。そのうえ、GoメソッドはC++かJavaよりも汎用的です。構造体だけでなく、あらゆる種類のデータに対して定義することができます。

.. Also, the lack of type hierarchy makes “objects” in Go feel much more lightweight than in languages such as C++ or Java.

また、型の階層がないということは、Goの"オブジェクト"はC++やJavaなどの他の言語よりも軽量である感覚を与えます。

.. How do I get dynamic dispatch of methods?

どのようにすれば動的ディスパッチが行えますか？
----------------------------------------------

.. The only way to have dynamically dispatched methods is through an interface. Methods on structs or other types are always resolved statically.

メソッドのダイナミックディスパッチを行う唯一の方法は、インタフェースを通じて呼び出す方法になります。構造体やその他の型に定義されたメソッドは、常に静的に解決されます。

.. Concurrent programming

並列プログラミング
==================

What operations are atomic? What about mutexes?
We haven't fully defined it all yet, but some details about atomicity are available in the Go Memory Model specification. Also, some concurrency questions are answered in more detail in the language design FAQ.

Regarding mutexes, the sync package implements them, but we hope Go programming style will encourage people to try higher-level techniques. In particular, consider structuring your program so that only one goroutine at a time is ever responsible for a particular piece of data.

Do not communicate by sharing memory. Instead, share memory by communicating.

Writing Code[Top]
How are libraries documented?
There is a program, godoc, written in Go, that extracts package documentation from the source code. It can be used on the command line or on the web. An instance is running at http://golang.org/pkg/. In fact, godoc implements the full site at http://golang.org/.

Is there a Go programming style guide?
Eventually, there may be a small number of rules to guide things like naming, layout, and file organization. The document Effective Go contains some style advice. More directly, the program gofmt is a pretty-printer whose purpose is to enforce layout rules; it replaces the usual compendium of do's and don'ts that allows interpretation. All the Go code in the repository has been run through gofmt.

How do I submit patches to the Go libraries?
The library sources are in go/src/pkg. If you want to make a significant change, please discuss on the mailing list before embarking.

See the document Contributing to the Go project for more information about how to proceed.

How do I create a multifile package?
Put all the source files for the package in a directory by themselves. Source files can refer to items from different files at will; there is no header file or need for forward declarations.

Other than being split into multiple files, the package will compile and test just like a single-file package.

How do I write a unit test?
Create a new file ending in _test.go in the same directory as your package sources. Inside that file, import "testing" and write functions of the form

.. code-block:: cpp

   func TestFoo(t *testing.T) {
       ...
   }

Run gotest in that directory. That script finds the Test functions, builds a test binary, and runs it.

Where is assert?
Go doesn't provide assertions. They are undeniably convenient, but our experience has been that programmers use them as a crutch to avoid thinking about proper error handling and reporting. Proper error handling means that servers continue operation after non-fatal errors instead of crashing. Proper error reporting means that errors are direct and to the point, saving the programmer from interpreting a large crash trace. Precise errors are particularly important when the programmer seeing the errors is not familiar with the code.

The same arguments apply to the use of assert() in test programs. Proper error handling means letting other tests run after one has failed, so that the person debugging the failure gets a complete picture of what is wrong. It is more useful for a test to report that isPrime gives the wrong answer for 2, 3, 5, and 7 (or for 2, 4, 8, and 16) than to report that isPrime gives the wrong answer for 2 and therefore no more tests were run. The programmer who triggers the test failure may not be familiar with the code that fails. Time invested writing a good error message now pays off later when the test breaks.

In testing, if the amount of extra code required to write good errors seems repetitive and overwhelming, it might work better as a table-driven test instead. Go has excellent support for data structure literals.

We understand that this is a point of contention. There are many things in the Go language and libraries that differ from modern practices, simply because we feel it's sometimes worth trying a different approach.

Implementation[Top]
What compiler technology is used to build the compilers?
Gccgo has a C++ front-end with a recursive descent parser coupled to the standard GCC back end. Gc is written in C using yacc/bison for the parser. Although it's a new program, it fits in the Plan 9 C compiler suite (http://plan9.bell-labs.com/sys/doc/compiler.html) and uses a variant of the Plan 9 loader to generate ELF binaries.

We considered writing 6g, the original Go compiler, in Go itself but elected not to do so because of the difficulties of bootstrapping and especially of open source distribution—you'd need a Go compiler to set up a Go environment. Gccgo, which came later, makes it possible to consider writing a compiler in Go, which might well happen. (Go would be a fine language in which to implement a compiler; a native lexer and parser are already available in /pkg/go.)

We also considered using LLVM for 6g but we felt it was too large and slow to meet our performance goals.

How is the runtime implemented?
Again due to bootstrapping issues, the runtime is mostly in C (with a tiny bit of assembler) although Go is capable of implementing most of it now. Gccgo's runtime uses glibc. Gc uses a custom library, to keep the footprint under control; it is compiled with a version of the Plan 9 C compiler that supports segmented stacks for goroutines. Work is underway to provide the same stack management in gccgo.
