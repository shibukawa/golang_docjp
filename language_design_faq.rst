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

**What are Go's ancestors?**

Go is mostly in the C family (basic syntax), with significant input from the Pascal/Modula/Oberon family (declarations, packages), plus some ideas from languages inspired by Tony Hoare's CSP, such as Newsqueak and Limbo (concurrency). However, it is a new language across the board. In every respect the language was designed by thinking about what programmers do and how to make programming, at least the kind of programming we do, more effective, which means more fun.

**What are the guiding principles in the design?**

Programming today involves too much bookkeeping, repetition, and clerical work. As Dick Gabriel says, “Old programs read like quiet conversations between a well-spoken research worker and a well-studied mechanical colleague, not as a debate with a compiler. Who'd have guessed sophistication bought such noise?” The sophistication is worthwhile—no one wants to go back to the old languages—but can it be more quietly achieved?

Go attempts to reduce the amount of typing in both senses of the word. Throughout its design, we have tried to reduce clutter and complexity. There are no forward declarations and no header files; everything is declared exactly once. Initialization is expressive, automatic, and easy to use. Syntax is clean and light on keywords. Stuttering (foo.Foo* myFoo = new(foo.Foo)) is reduced by simple type derivation using the := declare-and-initialize construct. And perhaps most radically, there is no type hierarchy: types just are, they don't have to announce their relationships. These simplifications allow Go to be expressive yet comprehensible without sacrificing, well, sophistication.

Another important principle is to keep the concepts orthogonal. Methods can be implemented for any type; structures represent data while interfaces represent abstraction; and so on. Orthogonality makes it easier to understand what happens when things combine.

Changes from C[Top]
Why is the syntax so different from C?
Other than declaration syntax, the differences are not major and stem from two desires. First, the syntax should feel light, without too many mandatory keywords, repetition, or arcana. Second, the language has been designed to be easy to analyze and can be parsed without a symbol table. This makes it much easier to build tools such as debuggers, dependency analyzers, automated documentation extractors, IDE plug-ins, and so on. C and its descendants are notoriously difficult in this regard.

Why are declarations backwards?
They're only backwards if you're used to C. In C, the notion is that a variable is declared like an expression denoting its type, which is a nice idea, but the type and expression grammars don't mix very well and the results can be confusing; consider function pointers. Go mostly separates expression and type syntax and that simplifies things (using prefix * for pointers is an exception that proves the rule). In C, the declaration

.. code-block:: c

	int* a, b;

declares a to be a pointer but not b; in Go

.. code-block:: cpp

	var a, b *int;

declares both to be pointers. This is clearer and more regular. Also, the := short declaration form argues that a full variable declaration should present the same order as := so

.. code-block:: cpp

	var a uint64 = 1;

has the same effect as

.. code-block:: cpp

	a := uint64(1);

Parsing is also simplified by having a distinct grammar for types that is not just the expression grammar; keywords such as func and chan keep things clear.

Why is there no pointer arithmetic?
Safety. Without pointer arithmetic it's possible to create a language that can never derive an illegal address that succeeds incorrectly. Compiler and hardware technology have advanced to the point where a loop using array indices can be as efficient as a loop using pointer arithmetic. Also, the lack of pointer arithmetic can simplify the implementation of the garbage collector.

Why are ++ and -- statements and not expressions? And why postfix, not prefix?
Without pointer arithmetic, the convenience value of pre- and postfix increment operators drops. By removing them from the expression hierarchy altogether, expression syntax is simplified and the messy issues around order of evaluation of ++ and -- (consider f(i++) and p[i] = q[++i]) are eliminated as well. The simplification is significant. As for postfix vs. prefix, either would work fine but the postfix version is more traditional; insistence on prefix arose with the STL, a library for a language whose name contains, ironically, a postfix increment.

Why do garbage collection? Won't it be too expensive?
One of the biggest sources of bookkeeping in systems programs is memory management. We feel it's critical to eliminate that programmer overhead, and advances in garbage collection technology in the last few years give us confidence that we can implement it with low enough overhead and no significant latency. (The current implementation is a plain mark-and-sweep collector but a replacement is in the works.)

Another point is that a large part of the difficulty of concurrent and multi-threaded programming is memory management; as objects get passed among threads it becomes cumbersome to guarantee they become freed safely. Automatic garbage collection makes concurrent code far easier to write. Of course, implementing garbage collection in a concurrent environment is itself a challenge, but meeting it once rather than in every program helps everyone.

Finally, concurrency aside, garbage collection makes interfaces simpler because they don't need to specify how memory is managed across them.

Absent features[Top]
Why does Go not have generic types?
Generics may well be added at some point. We don't feel an urgency for them, although we understand some programmers do.

Generics are convenient but they come at a cost in complexity in the type system and run-time. We haven't yet found a design that gives value proportionate to the complexity, although we continue to think about it. Meanwhile, Go's built-in maps and slices, plus the ability to use the empty interface to construct containers (with explicit unboxing) mean in many cases it is possible to write code that does what generics would enable, if less smoothly.

This remains an open issue.

Why does Go not have exceptions?
Exceptions are a similar story. A number of designs for exceptions have been proposed but each adds significant complexity to the language and run-time. By their very nature, exceptions span functions and perhaps even goroutines; they have wide-ranging implications. There is also concern about the effect they would have on the libraries. They are, by definition, exceptional yet experience with other languages that support them show they have profound effect on library and interface specification. It would be nice to find a design that allows them to be truly exceptional without encouraging common errors to turn into special control flow that requires every programmer to compensate.

Like generics, exceptions remain an open issue.

Why does Go not have assertions?
This is answered in the general FAQ.

Types[Top]
Why is there no type inheritance?
Object-oriented programming, at least in the best-known languages, involves too much discussion of the relationships between types, relationships that often could be derived automatically. Go takes a different approach.

Rather than requiring the programmer to declare ahead of time that two types are related, in Go a type automatically satisfies any interface that specifies a subset of its methods. Besides reducing the bookkeeping, this approach has real advantages. Types can satisfy many interfaces at once, without the complexities of traditional multiple inheritance. Interfaces can be very lightweight—having one or even zero methods in an interface can express useful concepts. Interfaces can be added after the fact if a new idea comes along or for testing—without annotating the original types. Because there are no explicit relationships between types and interfaces, there is no type hierarchy to manage or discuss.

It's possible to use these ideas to construct something analogous to type-safe Unix pipes. For instance, see how fmt.Fprintf enables formatted printing to any output, not just a file, or how the bufio package can be completely separate from file I/O, or how the crypto packages stitch together block and stream ciphers. All these ideas stem from a single interface (io.Writer) representing a single method (Write). And that's only scratching the surface.

It takes some getting used to but this implicit style of type dependency is one of the most exciting things about Go.

Why is len a function and not a method?
We debated this issue but decided implementing len and friends as functions was fine in practice and didn't complicate questions about the interface (in the Go type sense) of basic types.

Why does Go not support overloading of methods and operators?
Method dispatch is simplified if it doesn't need to do type matching as well. Experience with other languages told us that having a variety of methods with the same name but different signatures was occasionally useful but that it could also be confusing and fragile in practice. Matching only by name and requiring consistency in the types was a major simplifying decision in Go's type system.

Regarding operator overloading, it seems more a convenience than an absolute requirement. Again, things are simpler without it.

Values[Top]
Why does Go not provide implicit numeric conversions?
The convenience of automatic conversion between numeric types in C is outweighed by the confusion it causes. When is an expression unsigned? How big is the value? Does it overflow? Is the result portable, independent of the machine on which it executes? It also complicates the compiler; “the usual arithmetic conversions” are not easy to implement and inconsistent across architectures. For reasons of portability, we decided to make things clear and straightforward at the cost of some explicit conversions in the code. The definition of constants in Go—arbitrary precision values free of signedness and size annotations—ameliorates matters considerably, though.

A related detail is that, unlike in C, int and int64 are distinct types even if int is a 64-bit type. The int type is generic; if you care about how many bits an integer holds, Go encourages you to be explicit.

Why are maps built in?
The same reason strings are: they are such a powerful and important data structure that providing one excellent implementation with syntactic support makes programming more pleasant. We believe that Go's implementation of maps is strong enough that it will serve for the vast majority of uses. If a specific application can benefit from a custom implementation, it's possible to write one but it will not be as convenient syntactically; this seems a reasonable tradeoff.

Why don't maps allow structs and arrays as keys?
Map lookup requires an equality operator, which structs and arrays do not implement. They don't implement equality because equality is not well defined on such types; there are multiple considerations involving shallow vs. deep comparison, pointer vs. value comparison, how to deal with recursive structures, and so on. We may revisit this issue—and implementing equality for structs and arrays will not invalidate any existing programs—but without a clear idea of what equality of structs and arrays should mean, it was simpler to leave it out for now.

Why are maps, slices, and channels references while arrays are values?
There's a lot of history on that topic. Early on, maps and channels were syntactically pointers and it was impossible to declare or use a non-pointer instance. Also, we struggled with how arrays should work. Eventually we decided that the strict separation of pointers and values made the language harder to use. Introducing reference types, including slices to handle the reference form of arrays, resolved these issues. Reference types add some regrettable complexity to the language but they have a large effect on usability: Go became a more productive, comfortable language when they were introduced.

Concurrency[Top]
Why build concurrency on the ideas of CSP?
Concurrency and multi-threaded programming have a reputation for difficulty. We believe the problem is due partly to complex designs such as pthreads and partly to overemphasis on low-level details such as mutexes, condition variables, and even memory barriers. Higher-level interfaces enable much simpler code, even if there are still mutexes and such under the covers.

One of the most successful models for providing high-level linguistic support for concurrency comes from Hoare's Communicating Sequential Processes, or CSP. Occam and Erlang are two well known languages that stem from CSP. Go's concurrency primitives derive from a different part of the family tree whose main contribution is the powerful notion of channels as first class objects.

Why goroutines instead of threads?
Goroutines are part of making concurrency easy to use. The idea, which has been around for a while, is to multiplex independently executing functions—coroutines, really—onto a set of threads. When a coroutine blocks, such as by calling a blocking system call, the run-time automatically moves other coroutines on the same operating system thread to a different, runnable thread so they won't be blocked. The programmer sees none of this, which is the point. The result, which we call goroutines, can be very cheap: unless they spend a lot of time in long-running system calls, they cost little more than the memory for the stack.

To make the stacks small, Go's run-time uses segmented stacks. A newly minted goroutine is given a few kilobytes, which is almost always enough. When it isn't, the run-time allocates (and frees) extension segments automatically. The overhead averages about three cheap instructions per function call. It is practical to create hundreds of thousands of goroutines in the same address space. If goroutines were just threads, system resources would run out at a much smaller number.

Why are map operations not defined to be atomic?
After long discussion it was decided that the typical use of maps did not require safe access from multiple threads, and in those cases where it did, the map was probably part of some larger data structure or computation that was already synchronized. Therefore requiring that all map operations grab a mutex would slow down most programs and add safety to few. This was not an easy decision, however, since it means uncontrolled map access can crash the program.

The language does not preclude atomic map updates. When required, such as when hosting an untrusted program, the implementation could interlock map access.
