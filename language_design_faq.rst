.. _language_design_faq:

===========
言語設計FAQ
===========

.. Origins
.. =======

生立ち
======

.. **What is the history of the project?**

**このプロジェクトにはどのような歴史がありますか？**

.. Robert Griesemer, Rob Pike and Ken Thompson started sketching the goals for a new language on the white board on September 21, 2007. Within a few days the goals had settled into a plan to do something and a fair idea of what it would be. Design continued part-time in parallel with unrelated work. By January 2008, Ken had started work on a compiler with which to explore ideas; it generated C code as its output. By mid-year the language had become a full-time project and had settled enough to attempt a production compiler. In May 2008, Ian Taylor independently started on a GCC front end for Go using the draft specification. Russ Cox joined in late 2008 and helped move the language and libraries from prototype to reality.

Robert Griesemer、Rob PikeおよびKen THompsonは2007年9月21日にホワイトボードに新しい言語の目標をスケッチすることを始めました。数日かけて、数ある目標から、何をするのかという計画と、この言語の美しい理想像が定まりました。他の仕事と並行して、隙間時間を利用して設計が続けられました。2008年1月までに、Kenはコンパイラについてのアイディアを追求することを始めました。それは、コンパイラの出力としてCのコードを生成することでした。この年の中頃までに、この言語はフルタイムのプロジェクトとなり、製品版コンパイラを製作するために十分な時間を費やすことになりました。2008年5月には、Ian Taylorが自主的に、ドラフト版の仕様をもとにして、GCC用のフロントエンド作りに取りかかりました。Russ Coxは2008年の終わり頃に参加し、言語とライブラリをプロトタイプから現実的な実装に導くことを助けました。


.. Many others have contributed ideas, discussions, and code.

ほかにも沢山の人々にアイディアを出していただき、議論をし、ソースコードに寄与していただきました。

.. **Why are you creating a new language?**

**なぜ新しい言語を作るのですか？**

.. Go was born out of frustration with existing languages and environments for systems programming. Programming had become too difficult and the choice of languages was partly to blame. One had to choose either efficient compilation, efficient execution, or ease of programming; all three were not available in the same mainstream language. Programmers who could were choosing ease over safety and efficiency by moving to dynamically typed languages such as Python and JavaScript rather than C++ or, to a lesser extent, Java.

Goは、既存のシステムプログラミング用言語と環境への欲求不満から生まれた言語です。プログラミングはとても難しいものとなってしまいましたが、それは言語の選択が一因でもあります。効率的なコンパイル、効率的な実行、または容易なプログラミングのいずれかを選ばなければなりません。なぜなら、主要言語において、これら3つを満たす言語は無かったからです。プログラマは、安全性と効率を楽に得ることを選択するために、C++ではなく、PythonやJavaScriptのような動的型付け言語や、C++より自由度を下げて拡張したJavaに乗り換えることが可能ではありました。

.. Go is an attempt to combine the ease of programming of an interpreted, dynamically typed language with the efficiency and safety of a statically typed, compiled language. It also aims to be modern, with support for networked and multicore computing. Finally, it is intended to be fast: it should take at most a few seconds to build a large executable on a single computer. To meet these goals required addressing a number of linguistic issues: an expressive but lightweight type system; concurrency and garbage collection; rigid dependency specification; and so on. These cannot be addressed well by libraries or tools; a new language was called for.

Goは、インタプリタ型の動的型付け言語の容易なプログラミングと、コンパイラ型の静的型付け言語の効率と安全性を兼ね備える試みです。また、現代的であり、ネットワークコンピューティングおよびマルチコアコンピューティングをサポートする狙いがあります。最後に、高速な実行を狙いとしています。その上で、1つのコンピュータで大きな実行可能ファイルをビルドすることが数秒で完了するのは当然のことです。これらの目標を実現するためには、数々の言語上の課題に取り組まなくてはなりません。表現力が豊かで軽量なシステム、同時並行性とガーベジコレクション、堅牢な依存関係の仕様、などなどを実現する必要があります。これらはライブラリやツールで上手に解決することは不可能です。ゆえに、新しい言語が生まれたのです。

.. **What are Go's ancestors?**

**Goの祖先は何ですか？**

.. Go is mostly in the C family (basic syntax), with significant input from the Pascal/Modula/Oberon family (declarations, packages), plus some ideas from languages inspired by Tony Hoare's CSP, such as Newsqueak and Limbo (concurrency). However, it is a new language across the board. In every respect the language was designed by thinking about what programmers do and how to make programming, at least the kind of programming we do, more effective, which means more fun.

Goは基本的な文法の大部分はC言語系から受け継いでいます。また、宣言やパッケージについては、Pascal、Modula、Oberonから大きな影響を受けています。加えて、並行処理については、Tony HoareのCSP (Communicating Sequential Process) に影響を受けている言語であるNewsqueak、Limboから同じアイディアを取り入れています。しかしながら、全体としては新しい言語です。あらゆる点において、Goはプログラマが何をし、どうプログラムを作るのかということを考えられて設計されています。少なくとも私たち自身が行うプログラミングがより効率的に、より楽しくあるようにです。

**What are the guiding principles in the design?**

Programming today involves too much bookkeeping, repetition, and clerical work. As Dick Gabriel says, “Old programs read like quiet conversations between a well-spoken research worker and a well-studied mechanical colleague, not as a debate with a compiler. Who'd have guessed sophistication bought such noise?” The sophistication is worthwhile—no one wants to go back to the old languages—but can it be more quietly achieved?

Go attempts to reduce the amount of typing in both senses of the word. Throughout its design, we have tried to reduce clutter and complexity. There are no forward declarations and no header files; everything is declared exactly once. Initialization is expressive, automatic, and easy to use. Syntax is clean and light on keywords. Stuttering (foo.Foo* myFoo = new(foo.Foo)) is reduced by simple type derivation using the := declare-and-initialize construct. And perhaps most radically, there is no type hierarchy: types just are, they don't have to announce their relationships. These simplifications allow Go to be expressive yet comprehensible without sacrificing, well, sophistication.

Another important principle is to keep the concepts orthogonal. Methods can be implemented for any type; structures represent data while interfaces represent abstraction; and so on. Orthogonality makes it easier to understand what happens when things combine.

.. Changes from C

C言語からの変化点
====================

.. Why is the syntax so different from C?

なぜC言語とこんなに文法が違うんですか？
------------------------------------------

.. Other than declaration syntax, the differences are not major and stem from two desires. First, the syntax should feel light, without too many mandatory keywords, repetition, or arcana. Second, the language has been designed to be easy to analyze and can be parsed without a symbol table. This makes it much easier to build tools such as debuggers, dependency analyzers, automated documentation extractors, IDE plug-ins, and so on. C and its descendants are notoriously difficult in this regard.

宣言文を除くと、違いは大きくはありません。2つの希望によってこのようになっています。まず最初に、文法は軽く感じるようにすべきで、義務的なキーワードがあまりにも多かったり、繰り返し書かなければならなかったり、呪文のようだったりというのは辞めたいと考えていました。二つ目は、プログラミング言語はシンボルテーブルをパースしなくても簡単に分析できるように設計されました。この設計方針の影で、デバッガー、依存関係のアナライザ、自動ドキュメント抽出ツール、IDEプラグインなどのツールの開発がより簡単になります。C言語とその子孫の言語はこの点が困難で悪名をとどろかせていました。

.. Why are declarations backwards?

なぜ型宣言が後方になったのですか？
----------------------------------

.. They're only backwards if you're used to C. In C, the notion is that a variable is declared like an expression denoting its type, which is a nice idea, but the type and expression grammars don't mix very well and the results can be confusing; consider function pointers. Go mostly separates expression and type syntax and that simplifies things (using prefix * for pointers is an exception that proves the rule). In C, the declaration

もしもC言語を使用してきたのであれば、後方だけになります。C言語では、変数は式のように宣言され、型が指示されます。これは良い考えで、型と式の文法がごちゃごちゃに混ざることはありませんが、実行結果は混乱することがあります。関数ポインタの例を思い出してください。Goでは式と型の文法はほぼ切り離されていて、シンプルになっています。ポインタのための*プリフィックスはこのルールの例外として拡張しています。C言語では以下の宣言があったとすると:

.. code-block:: c

	int* a, b;

.. declares a to be a pointer but not b; in Go

この宣言ではaはポインタになりますが、bはなりません。Goの場合は

.. code-block:: cpp

	var a, b *int;

.. declares both to be pointers. This is clearer and more regular. Also, the := short declaration form argues that a full variable declaration should present the same order as := so

このどちらもポインタになります。こちらの方が明快で、より整然としています。また、 := の短い宣言の形態についても、完全な変数宣言と同じ順序であるべきです。そのため

.. code-block:: cpp

	var a uint64 = 1;

.. has the same effect as

これと、以下の宣言は同じ意味になります。

.. code-block:: cpp

	a := uint64(1);

.. Parsing is also simplified by having a distinct grammar for types that is not just the expression grammar; keywords such as func and chan keep things clear.

型のための文法と、式の文法がまったく異なっているため、パースをするのが簡単になります。 ``func`` や ``chan`` といったキーワードも、文法を分かりやすく維持するのに役立っています。

.. Why is there no pointer arithmetic?
   -----------------------------------

なぜポインタ演算がないんですか？
--------------------------------

.. Safety. Without pointer arithmetic it's possible to create a language that can never derive an illegal address that succeeds incorrectly. Compiler and hardware technology have advanced to the point where a loop using array indices can be as efficient as a loop using pointer arithmetic. Also, the lack of pointer arithmetic can simplify the implementation of the garbage collector.

安全のためのです。ポインタ演算がないおかげで、不正なアドレスを算出するようなことができないような言語を作ることができました。コンパイラとハードウェアの技術が進歩したおかげでポインタ演算を使ってループで実現するのと同じぐらい効率的に、配列のインデックスを使ったループでアドレスをポイントすることができるようになりました。また、ポインタ演算がないおかげで、ガーベジコレクタの実装もシンプルになりました。

.. Why are ++ and -- statements and not expressions? And why postfix, not prefix?
   ------------------------------------------------------------------------------

なぜ ++ と - - と not が式なんですか？なぜ後置なんですか？前置ではないのですか？
-------------------------------------------------------------------------------------

.. Without pointer arithmetic, the convenience value of pre- and postfix increment operators drops. By removing them from the expression hierarchy altogether, expression syntax is simplified and the messy issues around order of evaluation of ++ and -- (consider f(i++) and p[i] = q[++i]) are eliminated as well. The simplification is significant. As for postfix vs. prefix, either would work fine but the postfix version is more traditional; insistence on prefix arose with the STL, a library for a language whose name contains, ironically, a postfix increment.

ポインタ演算がないので、使い勝手の良い、値の前置、後置のインクリメント演算子はなくなりました。式の階層構造からこれらが取り除かれたことによって、式の文法はシンプルになり、++と--の評価順に関する複雑な問題(``f(i++)`` と ``p[i] = q[++i]`` の式がいつiを増やすのかという問題)をうまく除去することができます。シンプルというのは重要なことです。後置か前置かという点に関しては、どちらもうまく動作しますが、後置バージョンの方が伝統的に使われてきました。名前に後置のインクリメントを含む言語では、そのライブラリのSTLでは、皮肉なことに、前置を強制させていました。

.. Why do garbage collection? Won't it be too expensive?
   -----------------------------------------------------

なぜガーベジコレクタを動作させるのですか？実行コストが高いのではないですか？
-----------------------------------------------------------------------------

.. One of the biggest sources of bookkeeping in systems programs is memory management. We feel it's critical to eliminate that programmer overhead, and advances in garbage collection technology in the last few years give us confidence that we can implement it with low enough overhead and no significant latency. (The current implementation is a plain mark-and-sweep collector but a replacement is in the works.)

システムプログラミングのおいて、定型的な作業の中でもっともソースコードを占めているものの一つがメモリ管理です。私たちは、プログラマーのオーバーヘッドを取り除くことが重要だと考えています。ここ数年のガーベジコレクタの技術の進歩により、システムプログラムを作る上で作業上のオーバーヘッドを十分に減らしつつ、実行時に大きな遅延の影響を受けることもなくなりました。現在の実装は単純なマーク・アンド・スイープのガーベジコレクタを使用していますが、現在置き換えの作業を行っています。

.. Another point is that a large part of the difficulty of concurrent and multi-threaded programming is memory management; as objects get passed among threads it becomes cumbersome to guarantee they become freed safely. Automatic garbage collection makes concurrent code far easier to write. Of course, implementing garbage collection in a concurrent environment is itself a challenge, but meeting it once rather than in every program helps everyone.

もう一つのポイントは、並列のマルチスレッドプログラミングの難しさの大部分を占めているのがメモリ管理だ、という点です。オブジェクトがスレッド間でやりとりされはじめると、そのオブジェクトを安全に開放できるかどうか保証するのが難しくなります。自動ガーベジコレクションを適用することで、並列プログラミングのコードを書くのが極めて簡単になります。もちろん、並列環境でのガーベジコレクションを実装するのは、それ自身、一つのチャレンジではあるのですが、この一回の努力は、全てのプログラム、すべての人を助けることになると考えています。

.. Finally, concurrency aside, garbage collection makes interfaces simpler because they don't need to specify how memory is managed across them.

並列性の問題は置いておいても、ガーベジコレクタのおかげで、最終的にソースコード中のインタフェースはシンプルになっていきます。インタフェースのこちらと向こうで、メモリをどう管理するのか？というのを指定する必要がなくなるからです。

.. What's up with Unicode identifiers?
   ===================================

ユニコード識別子って何のことですか？
=====================================

.. It was important to us to extend the space of identifiers from the confines of ASCII. Go's rule—identifier characters must be letters or digits as defined by Unicode—is simple to understand and to implement but has restrictions. Combining characters are excluded by design, for instance. Until there is an agreed external definition of what an identifier might be, plus a definition of canonicalization of identifiers that guarantees no ambiguity, it seemed better to keep combining characters out of the mix. Thus we have a simple rule that can be expanded later without breaking programs, one that avoids bugs that would surely arise from a rule that admits ambiguous identifiers.

ASCIIの範囲内の空間から識別子を広げるというのは私たちにとって重要な課題です。識別子の文字はユニコードで規定されている文字か数字でなければならないというGoのルールは理解しやすく、実装も簡単ですが、制限がいくつかあります。例えば、設計上、 `合字 <http://ja.wikipedia.org/wiki/合字>`_ は除外されています。識別子が何であるかという外部定義があり、さらに正規化された識別しの定義があり、曖昧でないことが保証されるまでは、合字との組み合わせは除外しておいた方が良いと思っています。このため、文字空間をひろげつつ、あいまいな識別子を認めることによって発生しうるバグを避けるためのシンプルなルールを適用しています。

.. On a related note, since an exported identifier must begin with an upper-case letter, identifiers created from “letters” in some languages can, by definition, not be exported. For now the only solution is to use something like X日本語, which is clearly unsatisfactory; we are considering other options. The case-for-visibility rule is unlikely to change however; it's one of our favorite features of Go.

関連する項目として、exportされる識別子は大文字から始まってなければならないというものがあります。そのため、大文字や小文字がない言語の場合には、定義上、識別子をexportすることはできません。現状で唯一可能な解決策は ``X日本語`` というように定義することですが、明らかに良い方法とはいえません。私たちは現在別のオプションも検討しています。が、大文字にすると外部に見えるようになる、という識別子のルールは、私たちが気に入っているも機能でもあるので、今後も変わることはないでしょう。

Absent features
===============

Why does Go not have generic types?
Generics may well be added at some point. We don't feel an urgency for them, although we understand some programmers do.

Generics are convenient but they come at a cost in complexity in the type system and run-time. We haven't yet found a design that gives value proportionate to the complexity, although we continue to think about it. Meanwhile, Go's built-in maps and slices, plus the ability to use the empty interface to construct containers (with explicit unboxing) mean in many cases it is possible to write code that does what generics would enable, if less smoothly.

This remains an open issue.

Why does Go not have exceptions?
Exceptions are a similar story. A number of designs for exceptions have been proposed but each adds significant complexity to the language and run-time. By their very nature, exceptions span functions and perhaps even goroutines; they have wide-ranging implications. There is also concern about the effect they would have on the libraries. They are, by definition, exceptional yet experience with other languages that support them show they have profound effect on library and interface specification. It would be nice to find a design that allows them to be truly exceptional without encouraging common errors to turn into special control flow that requires every programmer to compensate.

Like generics, exceptions remain an open issue.

Why does Go not have assertions?
This is answered in the general FAQ.

.. Types

型
=====

.. Why is there no type inheritance?

なぜ型の継承が無いのですか？
-----------------------------

.. Object-oriented programming, at least in the best-known languages, involves too much discussion of the relationships between types, relationships that often could be derived automatically. Go takes a different approach.

オブジェクト指向のプログラミングにおいて、少なくとも良く知られている言語では、ほとんどの場合自動的に導きだせる型同士の関係について、多くの議論がなされてきました。GOは別のアプローチを取ります。


.. Rather than requiring the programmer to declare ahead of time that two types are related, in Go a type automatically satisfies any interface that specifies a subset of its methods. Besides reducing the bookkeeping, this approach has real advantages. Types can satisfy many interfaces at once, without the complexities of traditional multiple inheritance. Interfaces can be very lightweight—having one or even zero methods in an interface can express useful concepts. Interfaces can be added after the fact if a new idea comes along or for testing—without annotating the original types. Because there are no explicit relationships between types and interfaces, there is no type hierarchy to manage or discuss.

２種類の型の関係を宣言する時間はプログラマには必要なく、GOでは型は自動的にそのメソッドのサブセットが指定するインタフェースを全て満たします。設計書の削減に加え、このアプローチは実際に有利に働きます。型は伝統的な多重継承の複雑さを伴わずに、一度に多くのインタフェースを満たすことが出来ます。インタフェースはそのコンセプトを表現することができる１個ないし０個のメソッドを持つことで、とても軽量化することが出来ます。インタフェースは、新しいアイデアが出てたり、元の型の注釈を付けずにテストした後に、追加することが出来ます。なぜなら、型とインタフェースの間には明確な関係がありません、管理したり議論したりするための型の階層構造は存在しないのです。

.. It's possible to use these ideas to construct something analogous to type-safe Unix pipes. For instance, see how fmt.Fprintf enables formatted printing to any output, not just a file, or how the bufio package can be completely separate from file I/O, or how the crypto packages stitch together block and stream ciphers. All these ideas stem from a single interface (io.Writer) representing a single method (Write). And that's only scratching the surface.

これらのアイデアで、タイプセーフなUnixのパイプと類似したものを構築することが可能になります。例えば、fmt.Fprintfがどのようにファイルだけではない様々な出力に対してフォーマットを可能にしているのか、どのようにしてbufioパッケージがfile I/Oと完璧に区別されているか、どのようにしてcryptoパッケージがブロック暗号とストリーム暗号を縫い合わせているかを見てみてください。これら全てのアイデアは、単一のメソッド(Writer)を提供する単一のインタフェース(io.Writer)に根幹をなします。表面的な部分を触っているだけなのです。


.. It takes some getting used to but this implicit style of type dependency is one of the most exciting things about Go.

これはすこし慣れが必要かもしれませんが、この暗黙の型継承がこそがGOにおいて最もエキサイティングな点なのです。


.. Why is len a function and not a method?

なぜlenはメソッドではなく関数なのですか？
-------------------------------------------

.. We debated this issue but decided implementing len and friends as functions was fine in practice and didn't complicate questions about the interface (in the Go type sense) of basic types.

私たちはこの問題について議論しましたが、lenとその仲間をを関数として実装することにしました。それは、経験的に分かりやすく、基本的な型(Goにおいての型の意味)のインタフェースの問題を複雑にしないからです。



.. Why does Go not support overloading of methods and operators?

なぜGoはメソッドと演算子のオーバーライドをサポートしないのですか？
----------------------------------------------------------------------


.. Method dispatch is simplified if it doesn't need to do type matching as well. with other languages told us that having a variety of methods with the same name but different signatures was occasionally useful but that it could also be confusing and fragile in practice. Matching only by name and requiring consistency in the types was a major simplifying decision in Go's type system.

型のマッチングをする必要がない場合、メソッドの振り分けは簡素化できます。他の言語から分かることですが、同名で違う型宣言である関数は時として便利な反面、実際には混乱を招き、破綻しやすいことが有ります。型を名前のみでマッチングし、一貫性を必要とする方針は、Goの型システムを簡素化する主要な決定です。


.. Regarding operator overloading, it seems more a convenience than an absolute requirement. Again, things are simpler without it.

演算子のオーバーライドについては、絶対的な要件というよりも便利機能なように思えます。何度も言うように、無い方がシンプルになるということです。


.. Values

値
======

.. Why does Go not provide implicit numeric conversions?

なぜGoには暗黙的な数値変換がないのですか？
----------------------------------------------

.. The convenience of automatic conversion between numeric types in C is outweighed by the confusion it causes. When is an expression unsigned? How big is the value? Does it overflow? Is the result portable, independent of the machine on which it executes? It also complicates the compiler; “the usual arithmetic conversions” are not easy to implement and inconsistent across architectures. For reasons of portability, we decided to make things clear and straightforward at the cost of some explicit conversions in the code. The definition of constants in Go—arbitrary precision values free of signedness and size annotations—ameliorates matters considerably, though.

C言語における数値型での自動型変換の利便性は過剰であり、混乱を招く原因となっています。式が符号無しなのはいつか？ 値の大きさはどの位？ オーバーフローする？ 型変換の結果はポータブルで実行されるマシンに非依存？ これはコンパイラも複雑にします。「通常の算術変換」の実装は簡単ではなく、アーキテクチャ間で一貫性を保つことも簡単ではありません。私たちは移植性の理由から、コード中で明示的に型変換をする労力を払うことによって、明確で解り易くする決断をしました。それでも、Go言語での定数の定義(符号およびサイズのアノテーションの要らない任意精度の値)は問題をかなり改善しています。

.. A related detail is that, unlike in C, int and int64 are distinct types even if int is a 64-bit type. The int type is generic; if you care about how many bits an integer holds, Go encourages you to be explicit.

これと関連しますが、C言語とは違い、仮にintが64ビットだったとしても、intとint64は明確に別の型となります。int型はジェネリックです。整数が何ビットあるのかを気にしたい場合、Go言語では明示的に指定することが推奨されます。

.. Why are maps built in?

なぜmapは組み込みなのですか？
-----------------------------

.. The same reason strings are: they are such a powerful and important data structure that providing one excellent implementation with syntactic support makes programming more pleasant. We believe that Go's implementation of maps is strong enough that it will serve for the vast majority of uses. If a specific application can benefit from a custom implementation, it's possible to write one but it will not be as convenient syntactically; this seems a reasonable tradeoff.

stringと同じ理由です。これらは強力で重要なデータ構造であり、構文によるサポートがある優れた実装は、プログラミングをより快適にします。私たちはほぼすべての用途において、Go言語のmapの実装が十分に強力だと確信しています。特定のアプリケーション向けにカスタム実装が有益かもしれない場合、それを書くことはできるでしょうが、構文的に便利にはならないでしょう。妥当なトレードオフのようです。


.. Why don't maps allow structs and arrays as keys?

なぜmapは構造体や配列をキーとして許可していないのですか？
---------------------------------------------------------

.. Map lookup requires an equality operator, which structs and arrays do not implement. They don't implement equality because equality is not well defined on such types; there are multiple considerations involving shallow vs. deep comparison, pointer vs. value comparison, how to deal with recursive structures, and so on. We may revisit this issue—and implementing equality for structs and arrays will not invalidate any existing programs—but without a clear idea of what equality of structs and arrays should mean, it was simpler to leave it out for now.

mapのルックアップには等価演算子が必要となりますが、構造体および配列はこれを実装していません。実装していない理由は、このような型では等価演算子がwell-definedで無いためです。shallowあるいはdeepな比較か、ポインタあるいは値の比較か、再帰的な構造体をどう扱うか、などといった考慮すべき点が多数存在します。私たちはこの問題を再考(そして既存のプログラムを無効にすることのない構造体と配列用の等価演算子を実装)してもよいのですが、構造体や配列の等価演算子が何を意味すべきかについて明確なアイデアが無いので、いまのところ単に放置しておきます。

.. Why are maps, slices, and channels references while arrays are values?

なぜ配列は値なのに、map、slice、channelは参照なのですか？
---------------------------------------------------------

.. There's a lot of history on that topic. Early on, maps and channels were syntactically pointers and it was impossible to declare or use a non-pointer instance. Also, we struggled with how arrays should work. Eventually we decided that the strict separation of pointers and values made the language harder to use. Introducing reference types, including slices to handle the reference form of arrays, resolved these issues. Reference types add some regrettable complexity to the language but they have a large effect on usability: Go became a more productive, comfortable language when they were introduced.

この話題については多くの過去があります。初期の段階では、mapおよびchannelは構文的にはポインタであり、非ポインタのインスタンスとしての宣言や使用はできませんでした。また私たちは、配列がどう動作すべきか苦心していました。最終的には、ポインタと値を厳密に分離すると、言語が使いにくくなると判断しました。配列を参照の形で扱うsliceを含めて、参照型を導入することでこれらの問題を解決しました。参照型によって、言語に残念な複雑さが加わりましたが、使い易さにおいて大きな効果がありました。これらが導入された時点で、Go言語はより生産的で快適な言語になりました。

.. Concurrency

並列処理
===========

.. Why build concurrency on the ideas of CSP?

なぜ並列処理はCPSのアイデアを元にしたのでしょうか？
---------------------------------------------------

.. Concurrency and multi-threaded programming have a reputation for difficulty. We believe the problem is due partly to complex designs such as pthreads and partly to overemphasis on low-level details such as mutexes, condition variables, and even memory barriers. Higher-level interfaces enable much simpler code, even if there are still mutexes and such under the covers.

並列処理やマルチスレッドのプログラミングは難しいと言われています。私たちはこの問題がpthreadなどの複雑な設計や、mutex、条件変数、果てはメモリバリアといった低位レベルの詳細が強調され過ぎていることに、ある程度起因すると思っています。mutexなどが依然としてその裏に隠れていたとしても、高位レベルのインタフェースはコードをずっと単純にします。

.. One of the most successful models for providing high-level linguistic support for concurrency comes from Hoare's Communicating Sequential Processes, or CSP. Occam and Erlang are two well known languages that stem from CSP. Go's concurrency primitives derive from a different part of the family tree whose main contribution is the powerful notion of channels as first class objects.

言語上で高位レベルな並列処理のサポートを提供するモデルの最たる成功例に、HoareのCommunicating Sequential Processes(CSP)があります。OccamおよびErlangの２つは、CSPに由来するよく知られた言語です。Go言語の並列処理プリミティブは、これらと違う系統に由来するものであり、そのメインの提案はチャネルをファーストクラスオブジェクトとする強力な概念です。 

.. Why goroutines instead of threads?

なぜスレッドではなくgoroutineなのでしょうか？
----------------------------------------------

.. Goroutines are part of making concurrency easy to use. The idea, which has been around for a while, is to multiplex independently executing functions—coroutines, really—onto a set of threads. When a coroutine blocks, such as by calling a blocking system call, the run-time automatically moves other coroutines on the same operating system thread to a different, runnable thread so they won't be blocked. The programmer sees none of this, which is the point. The result, which we call goroutines, can be very cheap: unless they spend a lot of time in long-running system calls, they cost little more than the memory for the stack.

goroutineは並列処理を使い易くします。少し前からあたためていたそのアイデアとは、独立に実行する複数の関数(コルーチン)を、スレッドの集合に多重化することでした。ブロッキングするシステムコールを呼んだ場合などでコルーチンがブロックされる際に、ランタイムは同一スレッドにある他のコルーチンたちを別の実行可能なスレッドに自動的に移動して、それらがブロックされないようにします。プログラマはこの場面を見ることはありませんが、これこそが重要なのです。私たちがgoroutineと呼ぶこの仕組みは、非常に軽い処理にすることができます。実行時間の長いシステムコールで長時間費やさなければ、そのコストはスタック用のメモリ処理にかかるものより少し多い程度で済みます。

.. To make the stacks small, Go's run-time uses segmented stacks. A newly minted goroutine is given a few kilobytes, which is almost always enough. When it isn't, the run-time allocates (and frees) extension segments automatically. The overhead averages about three cheap instructions per function call. It is practical to create hundreds of thousands of goroutines in the same address space. If goroutines were just threads, system resources would run out at a much smaller number.

スタックを小さくするため、Go言語のランタイムはセグメント化されたスタックを使います。新しい出来立てのgoroutineでは数キロバイトが割り当てられますが、それはほとんどの場合で充分な大きさです。充分でない場合でも、ランタイムは追加でセグメントを自動的に割り当て(そして解放)します。関数呼び出しごとのオーバヘッドは、処理の軽い命令３つ分ぐらいが平均的なものです。同一アドレス空間で数十万規模のgoroutineを生成できるほど実用的なのです。もしgoroutineが単なるスレッドであったら、システムリソースはもっと小さな規模で枯渇してしまったでしょう。

.. Why are map operations not defined to be atomic?

なぜmapの処理はアトミックとして定義されていないのでしょうか？
-------------------------------------------------------------

.. After long discussion it was decided that the typical use of maps did not require safe access from multiple threads, and in those cases where it did, the map was probably part of some larger data structure or computation that was already synchronized. Therefore requiring that all map operations grab a mutex would slow down most programs and add safety to few. This was not an easy decision, however, since it means uncontrolled map access can crash the program.

長い議論の末、mapの典型的な使用では複数スレッドからのアクセスを安全に保護する必要性は無いと判断しました。そのような状況では、mapはほぼ確実に大きなデータ構造か計算の一部であり、すでに同期されています。そのためmapの処理すべてにおいてmutexを獲得すると、ほとんどのプログラムでスローダウンするものの、安全性の向上はあまり期待できません。しかしながら、mapへの制御されていないアクセスはプログラムをクラッシュさせ得ることを意味するため、簡単な議論ではありませんでした。

.. The language does not preclude atomic map updates. When required, such as when hosting an untrusted program, the implementation could interlock map access.

言語仕様上ではアトミックなmapの更新を排除していません。信頼できないプログラムを稼動させるなど、その必要性がある場合、mapへのアクセスを実装によって安全のためにインターロックすることも可能です。
