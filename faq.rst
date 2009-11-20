===
FAQ
===

.. とりあえずファイルだけ作って、最初の項目に装飾の付け方だけ設定しました。
   これを参考に日本語訳してください。渋川

Origins
=======

**What is the purpose of the project?**

No major systems language has emerged in over a decade, but over that time the computing landscape has changed tremendously. There are several trends:

* Computers are enormously quicker but software development is not faster.
* Dependency management is a big part of software development today but the “header files” of languages in the C tradition are antithetical to clean dependency analysis—and fast compilation.
* There is a growing rebellion against cumbersome type systems like those of Java and C++, pushing people towards dynamically typed languages such as Python and JavaScript.
* Some fundamental concepts such as garbage collection and parallel computation are not well supported by popular systems languages.
* The emergence of multicore computers has generated worry and confusion.

We believe it's worth trying again with a new language, a concurrent, garbage-collected language with fast compilation. Regarding the points above:

* It is possible to compile a large Go program in a few seconds on a single computer.
* Go provides a model for software construction that makes dependency analysis easy and avoids much of the overhead of C-style include files and libraries.
* Go's type system has no hierarchy, so no time is spent defining the relationships between types. Also, although Go has static types the language attempts to make types feel lighter weight than in typical OO languages.
* Go is fully garbage-collected and provides fundamental support for concurrent execution and communication.
* By its design, Go proposes an approach for the construction of system software on multicore machines.

**What is the origin of the name?**

“Ogle” would be a good name for a Go debugger.

**What kind of a name is 6g?**

The 6g (and 8g and 5g) compiler is named in the tradition of the Plan 9 C compilers, described in http://plan9.bell-labs.com/sys/doc/compiler.html (see the table in section 2). 6 is the architecture letter for amd64 (or x86-64, if you prefer), while g stands for Go.

**Why not just write some libraries for C++ to do communication?**

We considered doing that, but too many of the problems—lack of garbage collection, long dependency chains, nested include files, lack of concurrency awareness—are rooted in the design of the C and C++ languages themselves. We felt a viable solution required a more complete approach.

**Why doesn't Go run on Windows?**

We understand that a significant fraction of computers in the world run Windows and it would be great if those computers could run Go programs. However, the Go team is small and we don't have the resources to do a Windows port at the moment. We would be more than willing to answer questions and offer advice to anyone willing to develop a Windows version.

Usage
=====

**Who should use the language?**

Go is an experiment. We hope adventurous users will give it a try and see if they enjoy it. Not every programmer will, but we hope enough will find satisfaction in the approach it offers to justify further development.

Is Google using Go internally?
The Go project was conceived to make it easier to write the kind of servers and other software Google uses internally, but the implementation isn't quite mature enough yet for large-scale production use. While we continue development we are also doing experiments with the language as a candidate server environment. It's getting there. For instance, the server behind http://golang.org is a Go program; in fact it's just the godoc document server running in a production configuration.

Do Go programs link with C/C++ programs?
There are two Go compiler implementations, 6g and friends, generically called gc, and gccgo. Gc uses a different calling convention and linker and can therefore only be linked with C programs using the same convention. There is such a C compiler but no C++ compiler. Gccgo is a GCC front-end that can, with care, be linked with GCC-compiled C or C++ programs. However, because Go is garbage-collected it will be unwise to do so, at least naively.

There is a “foreign function interface” to allow safe calling of C-written libraries from Go code. We expect to use SWIG to extend this capability to C++ libraries. There is no safe way to call Go code from C or C++ yet.

Does Go support Google's protocol buffers?
Protocol buffers are supported. We plan to have the next release of the protocol buffer source code include Go code generators and a Go library for them. The implementation uses data reflection at run time so it is slow, but a new implementation is planned.

Design[Top]
Why doesn't Go have feature X?
Every language contains novel features and omits someone's favorite feature. Go was designed with an eye on felicity of programming, speed of compilation, orthogonality of concepts, and the need to support features such as concurrency and garbage collection. Your favorite feature may be missing because it doesn't fit, because it affects compilation speed or clarity of design, or because it would make the fundamental system model too difficult.

If it bothers you that Go is missing feature X, please forgive us and investigate the features that Go does have. You might find that they compensate in interesting ways for the lack of X.

Why is the syntax so different from C++?
This and other language design questions are answered in the separate language design FAQ.

Object-Oriented Programming[Top]
Is Go an object-oriented language?
Yes and no. Although Go has types and methods and allows an object-oriented style of programming, there is no type hierarchy. The concept of “interface” in Go provides a different approach that we believe is easy to use and in some ways more general. There are also ways to embed types in other types to provide something analogous—but not identical—to subclassing. Moreover, methods in Go are more general than in C++ or Java: they can be defined for any sort of data, not just structs.

Also, the lack of type hierarchy makes “objects” in Go feel much more lightweight than in languages such as C++ or Java.

How do I get dynamic dispatch of methods?
The only way to have dynamically dispatched methods is through an interface. Methods on structs or other types are always resolved statically.

Concurrent programming[Top]
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
