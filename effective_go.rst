.. _effective_go:

============
Effective Go
============

.. Introduction
   ============

イントロダクション
==================

.. Go is a new language. Although it borrows ideas from existing languages, it has unusual properties that make effective Go programs different in character from programs written in its relatives. A straightforward translation of a C++ or Java program into Go is unlikely to produce a satisfactory result?Java programs are written in Java, not Go. On the other hand, thinking about the problem from a Go perspective could produce a successful but quite different program. In other words, to write Go well, it's important to understand its properties and idioms. It's also important to know the established conventions for programming in Go, such as naming, formatting, program construction, and so on, so that programs you write will be easy for other Go programmers to understand.

Goは新しい言語です。既存の言語からいろいろなアイディアを借りてきていますが、効果的なGoプログラムは、関連するような言語のプログラムとはかなり異なる性質を持っています。C++やJavaなどのプログラムをそのままGoに変換しても、満足できるような結果にはなりません。所詮はJavaで書かれたプログラムであって、Goらしいプログラムにはなりません。一方、Goの視点からプログラムについて考えてみると、うまくいく可能性はありますが、既存のプログラムとは違う結果になるでしょう。言い換えると、Goらしいプログラムをうまく書くためには、Goの特性やイディオムを理解することが重要になります。また、命名規則、フォーマット、プログラムの構造などの、Goでプログラミングをするための適切な習慣を知ることも大切になります。そうすることで、他のGoプログラマが簡単にあなたのプログラムを読むことができるようになります。

.. This document gives tips for writing clear, idiomatic Go code. It augments the language specification and the tutorial, both of which you should read first.

このドキュメントは明快で、Goらしいコードを書くためのティップスを提供します。このドキュメントは言語仕様と、チュートリアルの知識を補完するものです。先にその両方のドキュメントを読んでください。

.. Examples
   --------

サンプル
--------

.. The Go package sources are intended to serve not only as the core library but also as examples of how to use the language. If you have a question about how to approach a problem or how something might be implemented, they can provide answers, ideas and background.

Goのパッケージのソースは、機能を提供するコアライブラリというだけではなく、Goを使用するためのサンプルにもなっています。問題に対して、どのようにアプローチをしようか疑問をもったり、これはどのように実装されているのか疑問を持った場合には、パッケージのソースを読むとその答え、アイディア、背景などが分かるでしょう。

.. Formatting
   ==========

フォーマッティング
==================

.. Formatting issues are the most contentious but the least consequential. People can adapt to different formatting styles but it's better if they don't have to, and less time is devoted to the topic if everyone adheres to the same style. The problem is how to approach this Utopia without a long prescriptive style guide.

フォーマットの問題は、よく議論を呼びますが、それほど重要な問題ではありません。開発者は普段と違うフォーマットのスタイルにも適応することはできますが、すべての開発者が同じスタイルを守っていれば、この議論に消費される時間を減らすことができます。この話題に時間をかけずに済むなら、そちらの方が望ましいでしょう。この場合に問題となるのは、規範となる、長いスタイルガイドを使わないで、このようなユートピアに近づいていくにはどのようにすればいいのか、ということです。

.. With Go we take an unusual approach and let the machine take care of most formatting issues. A program, gofmt, reads a Go program and emits the source in a standard style of indentation and vertical alignment, retaining and if necessary reformatting comments. If you want to know how to handle some new layout situation, run gofmt; if the answer doesn't seem right, fix the program (or file a bug), don't work around it.

Goの設計では、珍しい解決策を取りました。ほとんどのフォーマットに関する問題を自動でやらせるようにしました。 :program:`gofmt` というプログラムがあり、これはGoのプログラムを読み込んで、インデントと垂直方向の整列、必要ならコメントの再フォーマットも行って、標準的なフォーマットに書き直したソースを出力します。あたらしくレイアウトを行う場面で、 :program:`gofmt` を実行したときに、結果が正しくないと思われたら、プログラムを修正するか、バグを報告してください。そのまま行わないでください。

.. As an example, there's no need to spend time lining up the comments on the fields of a structure. Gofmt will do that for you. Given the declaration::

サンプルとして、時間を使うのがもったいない、フィールドのコメントの行構造の修正を行ってみます。 :program:`gofmt` が行ってくれることを見てみます。以下のような宣言文を渡します:

.. code-block:: cpp

  type T struct {
      name string; // オブジェクト名
      value int; // オブジェクトの値
  }

.. type T struct {
       name string; // name of the object
       value int; // its value
   }

.. gofmt will line up the columns:

:program:`gofmt` がカラムの行あげを行ってくれます:

.. code-block:: cpp

  type T struct {
      name    string; // オブジェクトの名前
      value   int;    // オブジェクトの値
  }

.. type T struct {
       name    string; // name of the object
       value   int;    // its value
   }

.. All code in the libraries has been formatted with gofmt.

ライブラリのすべてのコードは、 :program:`gofmt` を使ってフォーマット修正しています。

.. Some formatting details remain. Very briefly,

いくつかフォーマットに関する詳細が残っているので、手短に説明します。

.. Indentation
      We use tabs for indentation and gofmt emits them by default. Use spaces only if you must.

インデント
   Goではインデントにタブを使用します。 :program:`gofmt` もデフォルトではそのようなコードを出力します。スペースを使用しなければならない場合だけ、使用してください。

.. Line length
      Go has no line length limit. Don't worry about overflowing a punched card. If a line feels too long, wrap it and indent with an extra tab.

行の長さ
   Goでは行の長さの制限はありません。パンチカードのオーバーフローを心配する必要はありません。もしも長すぎると感じた場合には、改行して、追加のタブを入れてください。

.. Parentheses
       Go needs fewer parentheses: control structures (if, for, switch) do not require parentheses in their syntax. Also, the operator precedence hierarchy is shorter and clearer, so:

       means what the spacing implies.

カッコ
   Goはカッコを打つ回数が少なくて住むようになっています。文法上は制御構文(if, for , switch)も、カッコが不要です。また、演算子の優先順位の階層短くて明確なため:

   .. code-block:: cpp

      x<<8 + y<<16

   スペースが暗示しているとおりの順番になります。    

.. Commentary
   ==========

コメント
========

.. Go provides C-style /* */ block comments and C++-style // line comments. Line comments are the norm; block comments appear mostly as package comments and are also useful to disable large swaths of code.

GoはCスタイルの /\* \*/ ブロックコメントとC++スタイルの // 行コメントを提供します。行コメントは一般的に使用されるもので、ブロックコメントは大抵はパッケージのコメントでよく使われ、大部分のコードを一気に無効にする際にも便利です。

.. The program—and web server—godoc processes Go source files to extract documentation about the contents of the package. Comments that appear before top-level declarations, with no intervening newlines, are extracted along with the declaration to serve as explanatory text for the item. The nature and style of these comments determines the quality of the documentation godoc produces. 

プログラム、そしてWebサーバーでもある\ :program:`godoc`\ はGoソースファイルを処理してパッケージ内コンテンツのドキュメントを抽出します。トップレベルの宣言前にあり改行が間に入らないコメントはその宣言とともに抽出され、その項目の説明文となります。これらのコメントの特性とスタイルが\ :program:`godoc`\ が生成するドキュメントの質を決定します。

.. Every package should have a package comment, a block comment preceding the package clause. For multi-file packages, the package comment only needs to be present in one file, and any one will do. The package comment should introduce the package and provide information relevant to the package as a whole. It will appear first on the godoc page and should set up the detailed documentation that follows.

各パッケージはパッケージ節に先行するブロックコメントであるパッケージコメントを持つべきです。パッケージが複数のファイルにわたる場合はそのうち1つのファイルにしか必要ありません。パッケージコメントはそのパッケージの紹介と関連情報をまとめて提供するものであるべきです。これは\ :program:`godoc`\ ページの始めに表示されるので、それに続く詳細なドキュメントのお膳立てすべきです。

.. 


.. code-block:: cpp

   /*
   	The regexp package implements a simple library for
   	regular expressions.

   	The syntax of the regular expressions accepted is:

   	regexp:
   		concatenation { '|' concatenation }
   	concatenation:
   		{ closure }
   	closure:
   		term [ '*' | '+' | '?' ]
   	term:
   		'^'
   		'$'
   		'.'
   		character
   		'[' [ '^' ] character-ranges ']'
   		'(' regexp ')'
   */
   package regexp

.. If the package is simple, the package comment can be brief. 

パッケージがシンプルなものであれば、パッケージコメントはシンプルなもので良いでしょう。

.. code-block:: cpp

   // The path package implements utility routines for
   // manipulating slash-separated filename paths.

.. Comments do not need extra formatting such as banners of stars. The generated output may not even be presented in a fixed-width font, so don't depend on spacing for alignment—godoc, like gofmt, takes care of that. Finally, the comments are uninterpreted plain text, so HTML and other annotations such as _this_ will reproduce verbatim and should not be used.

コメントはアスタリスクのバナーのような余分なフォーマッティングをする必要はありません。生成される出力は等幅フォントで表示されないかもしれませんので、位置合わせのためのスペーシングに依存することがないようにします。\ :program:`godoc`\ は\ :program:`gofmt`\ のようにこの問題の面倒をみてくれます。最後に、コメントはインタープリタに処理されないプレインテキストなので、HTMLや_このような_註釈は文字どおりに表示されるので使用してはいけません。

.. Inside a package, any comment immediately preceding a top-level declaration serves as a doc comment for that declaration. Every exported (capitalized) name in a program should have a doc comment.

パッケージ内でトップレベルの宣言の直前にあるものはその宣言のDocコメントとなります。エクスポートされた(大文字で始まる)名前はdocコメントを持つべきです。

.. Doc comments work best as complete English sentences, which allow a wide variety of automated presentations. The first sentence should be a one-sentence summary that starts with the name being declared.

Docコメントは完全な英文で書くのが最も効果的です。これにより広範囲にわたる自動プレゼンテーションが可能となります。始めの文はこれから宣言される名前から始まる1行の要約であるべきです。

.. code-block:: cpp

   // Compile parses a regular expression and returns, if successful, a Regexp
   // object that can be used to match against text.
   func Compile(str string) (regexp *Regexp, error os.Error) {

.. Go's declaration syntax allows grouping of declarations. A single doc comment can introduce a group of related constants or variables. Since the whole declaration is presented, such a comment can often be perfunctory.

Goの宣言構文は宣言のグループ化が可能です。1つのDocコメントはある定数や変数のグループに対し使用することが出来ます。すべての宣言が提示されるので、そのようなコメントは形式的なものとなります。

.. code-block:: cpp

   // Error codes returned by failures to parse an expression.
   var (
       ErrInternal      = os.NewError("internal error");
       ErrUnmatchedLpar = os.NewError("unmatched '('");
       ErrUnmatchedRpar = os.NewError("unmatched ')'");
       ...
   )

.. Even for private names, grouping can also indicate relationships between items, such as the fact that a set of variables is protected by a mutex.

プライベートな名前に対してもグルーピングをしてそれらの関係、たとえばある変数群がmutexでで保護されているなどを示すことが可能です。

.. code-block:: cpp

   var (
       countLock    sync.Mutex;
       inputCount    uint32;
       outputCount    uint32;
       errorCount    uint32;
   )

.. Names
.. =====

名前
=====

.. Names are as important in Go as in any other language. In some cases they even have semantic effect: for instance, the visibility of a name outside a package is determined by whether its first character is upper case. It's therefore worth spending a little time talking about naming conventions in Go programs.

他の言語と同様に、Goにおいても名前は重要です。場合によって、名前は意味上の効果をもたらします。例えば、パッケージの外側にある名前の可視性は、名前の一文字目が大文字かどうかで決定されます。従って、命名規則にわずかばかりの時間を使うことは、Goにおいて価値のあることです。

.. Package names
.. -------------

パッケージ名
-------------

.. When a package is imported, the package name becomes an accessor for the contents. After::

..   import "bytes"

.. the importing package can talk about bytes.Buffer. It's helpful if everyone using the package can use the same name to refer to its contents, which implies that the package name should be good: short, concise, evocative. By convention, packages are given lower case, single-word names; there should be no need for underscores or mixedCaps. Err on the side of brevity, since everyone using your package will be typing that name. And don't worry about collisions a priori. The package name is only the default name for imports; it need not be unique across all source code, and in the rare case of a collision the importing package can choose a different name to use locally. In any case, confusion is rare because the file name in the import determines just which package is being used.

パッケージをインポートすると、パッケージ名はパッケージ内容へのアクセッサになります。次のように::

  import "bytes"

と書くと、インポートされるパッケージを通してbytes.Bufferを使用することができます。パッケージを使用する人々が、パッケージ内容を参照する際に同じ名前を使用することができれば、それは役に立ちます。このことは、パッケージ名が短く、簡潔で、意味明瞭なものほど良いことを意味します。規約により、パッケージには小文字の1単語である名前が与えられます。アンダースコアの使用や大文字小文字の混在は必要ありません。また、あなたのパッケージを使う人々が使う度にパッケージ名を打ち込むことを考えて、パッケージ名を簡潔過ぎるほど簡潔にしてしまう場合があります。その場合でも、名前の事前衝突を心配する必要はありません。なぜなら、パッケージ名はインポートするためのデフォルト名でしかないからです。すなわち、ソースコード全体でパッケージ名がユニークである必要はありません。万が一インポートされるパッケージ名が衝突する場合にも、局所的に異なるパッケージ名を選択することが可能です。どのような場合でも、インポート機能において、ファイル名がどのパッケージが使用されているかを決定するので、混乱することはまれです。

.. Another convention is that the package name is the base name of its source directory; the package in src/pkg/container/vector is imported as "container/vector" but has name vector, not container_vector and not containerVector.

もう一つの規約は、パッケージ名はそのソースファイルのディレクトリの基本名であるということです。src/pkg/container/vectorパッケージは、"container/vector"としてインポートされます。パッケージ名はvectorであって、container_vectorでもcontainerVectorでもありません。

.. The importer of a package will use the name to refer to its contents (the import . notation is intended mostly for tests and other unusual situations), so exported names in the package can use that fact to avoid stutter. For instance, the buffered reader type in the bufio package is called Reader, not BufReader, because users see it as bufio.Reader, which is a clear, concise name. Moreover, because imported entities are always addressed with their package name, bufio.Reader does not conflict with io.Reader. Similarly, the function to make new instances of vector.Vector?which is the definition of a constructor in Go?would normally be called NewVector, but since Vector is the only type exported by the package, and since the package is called vector, it's called just New. Clients of the package see that as vector.New. Use the package structure to help you choose good names.

パッケージインポータは、パッケージ内容を参照するためにパッケージ名を使用します（.（ドット）記法を使ったインポートは、テストや他のまれな場面で使用します）。

.. Another short example is once.Do; once.Do(setup) reads well and would not be improved by writing once.DoOrWaitUntilDone(setup). Long names don't automatically make things more readable. If the name represents something intricate or subtle, it's usually better to write a helpful doc comment than to attempt to put all the information into the name.

もう一つの短い例は、once.Doです。onche.Do(setup)は読みやすく、once.DoOrWaitUntileDone(setup)と書いてもより読みやすくなることはありません。より長い名前を使えば、より読みやすくなるということはありません。もし名前が難解であったり、捉えにくい場合、名前にすべての情報を詰め込もうとするより、役に立つDocコメントを記述するのがより良いのが普通です。

.. Interface names
.. ---------------

インターフェース名
------------------

.. By convention, one-method interfaces are named by the method name plus the -er suffix: Reader, Writer, Formatter etc.

規約により、1メソッドのインターフェースは、メソッド名に-erサフィックスを加えた名前を持ちます。例えば、Reader, Writer, Formatter などです。

.. There are a number of such names and it's productive to honor them and the function names they capture. Read, Write, Close, Flush, String and so on have canonical signatures and meanings. To avoid confusion, don't give your method one of those names unless it has the same signature and meaning. Conversely, if your type implements a method with the same meaning as a method on a well-known type, give it the same name and signature; call your string-converter method String not ToString.

このような名前は多く存在します。そして、その名前と、それらが得る機能名は、敬意を表するほど生産的です。Read, Write, Close, Flush, String などの名前は、標準的な特徴と意味を備えています。混乱を避けるため、あなたのメソッドが同じ特徴と意味を備えていない限り、そのような名前をメソッド名としてはなりません。逆に言えば、もしあなたがよく知られた種類の、同じ意味を持つメソッドを実装したならば、同じ名前と特徴を与えるべきです。例えば独自の文字列変換メソッドを実装するなら、そのメソッド名はToStringではなく、Stringであるべきです。

.. MixedCaps
.. ---------

大文字小文字の混在
------------------

.. Finally, the convention in Go is to use MixedCaps or mixedCaps rather than underscores to write multiword names.

最後に、Goにおける規約では、複数単語の名前を記述する際、アンダースコアを使用するのではなく、MixedCapsまたはmixedCapsと記述するように決められています。

.. Semicolons
   ==========

セミコロン
----------

.. Go needs fewer semicolons between statements than do other C variants. Semicolons are never required at the top level. And they are separators, not terminators, so they can be left off the last element of a statement or declaration list, a convenience for one-line funcs and the like::

Goは他のCの異型のように多くのセミコロンを必要としません。セミコロンはトップレベルでは使う必要はありません。また終端記号ではなく分離記号なので、文や宣言のリストの最後では省くことが可能で、一行の関数などでの使用に便利です。

.. code-block:: cpp

  func CopyInBackground(dst, src chan Item) {
      go func() { for { dst <- <-src } }()
  }

.. In fact, semicolons can be omitted at the end of any "StatementList" in the grammar, which includes things like cases in switch statements::

事実、セミコロンは文法上のいかなる"文のリスト"でも省略することが可能です。これはswitch文でのcaseを含みます。

.. code-block:: cpp

  switch {
  case a < b:
      return -1
  case a == b:
      return 0
  case a > b:
      return 1
  }


.. The grammar accepts an empty statement after any statement list, which means a terminal semicolon is always OK. As a result, it's fine to put semicolons everywhere you'd put them in a C program?they would be fine after those return statements, for instance?but they can often be omitted. By convention, they're always left off top-level declarations (for instance, they don't appear after the closing brace of struct declarations, or of funcs for that matter) and often left off one-liners. But within functions, place them as you see fit.

文法上、すべての文のリストのあとに空文を許されているので、終端にセミコロンを使ってもかまいません。結果としてCでセミコロンを使う場所で使っても問題ありませんし、たとえばこれらのreturnの後に置いてもかまいませんが、通常は省略されます。慣習上、セミコロンはトップレベルの宣言では省略されます(たとえばstructやfuncの宣言の閉じ括弧にセミコロンは使われません)し、ワンライナーでもそうですが、関数の中では適切だと思うところで使ってください。


.. Control structures
   ==================

制御構造
========

.. The control structures of Go are related to those of C but different in important ways. There is no do or while loop, only a slightly generalized for; switch is more flexible; if and switch accept an optional initialization statement like that of for; and there are new control structures including a type switch and a multiway communications multiplexer, select. The syntax is also slightly different: parentheses are not required and the bodies must always be brace-delimited.

Goの制御構造はCのものと関係がありますが、重要な点で異なります。doやwhileループがなく、少々一般化されたforだけがあり、switchはより柔軟で、ifやswitchではforのように任意の初期化文を使え、そしてtype switchやmultiway communications multiplexerやselectなどの新規の制御構造があります。文法も少し異なります。括弧が必須ではなく、本文は必ず中括弧で囲まれていなければなりません。

If
--

.. In Go a simple if looks like this::

Goでは単純なifは次のようなものです。

.. code-block:: cpp

  if x > 0 {
      return y
  }

.. Mandatory braces encourage writing simple if statements on multiple lines. It's good style to do so anyway, especially when the body contains a control statement such as a return or break.

中括弧を強制することにより単純なif文を複数行にわたって書くよう促します。これは良いスタイルですが、本体にreturnやbreakなどの制御文が含まれる場合は特にそうです。

.. Since if and switch accept an initialization statement, it's common to see one used to set up a local variable::

ifやswitchは初期化文を使えるので、ローカル変数をセットアップする際によく使われます。

.. code-block:: cpp

   if err := file.Chmod(0664); err != nil {
       log.Stderr(err);
       return err;
   }

.. In the Go libraries, you'll find that when an if statement doesn't flow into the next statement—that is, the body ends in break, continue, goto, or return—the unnecessary else is omitted. 

Goのライブラリでは、if文で次の文に処理が進まない時、つまり本体がbreakやcontinue、goto、returnなどで終わる時、不要であるelseは省略されます。

.. code-block:: cpp

   f, err := os.Open(name, os.O_RDONLY, 0);
   if err != nil {
       return err;
   }
   codeUsing(f);

.. This is a example of a common situation where code must analyze a sequence of error possibilities. The code reads well if the successful flow of control runs down the page, eliminating error cases as they arise. Since error cases tend to end in return statements, the resulting code needs no else statements::

これはエラーの発生を順にチェックしなければならない場合によく見られる例です。正常系のフローはページを下がっていくもので、エラーケースは発生のたびに打ち切られるようなコードは読みやすいものです。この場合エラーケースは通常return文で終わるので、結果としてelse文は不要となります。

.. code-block:: cpp

   f, err := os.Open(name, os.O_RDONLY, 0);
   if err != nil {
       return err;
   }
   d, err := f.Stat();
   if err != nil {
       return err;
   }
   codeUsing(f, d);


For
---

.. The Go for loop is similar to—but not the same as—C's. It unifies for and while and there is no do-while. There are three forms, only one of which has semicolons.

GoのforはCのものと似ていますが同じではありません。それはforとwhileを1つにしたものでdo-whileはありません。forは3つの形式があり、そのうち1つだけがセミコロンを使います。

.. code-block:: cpp

   // Cのforのようなもの
   for init; condition; post { }
  
   // Cのwhileのようなもの
   for condition { }

   // Cのfor(;;)のようなもの
   for { }

.. // Like a C for
.. // Like a C while
.. // Like a C for(;;)

.. Short declarations make it easy to declare the index variable right in the loop::

宣言が短いのでインデックス変数をループの中で簡単に宣言出来ます。

.. code-block:: cpp

   sum := 0;
   for i := 0; i < 10; i++ {
       sum += i
   }

.. If you're looping over an array, slice, string, or map, or reading from a channel, a range clause can manage the loop for you.

配列、スライス、マップなどをループする時やチャンネルから読み込む時にはrange節を使ってループをうまく使うことが出来ます。

.. code-block:: cpp

   var m map[string]int;
   sum := 0;
   for _, value := range m {  // キーは未使用
       sum += value
   }

..  // key is unused

.. For strings, the range does more work for you, breaking out individual Unicode characters by parsing the UTF-8 (erroneous encodings consume one byte and produce the replacement rune U+FFFD). The loop

文字列に対してはrangeはなお便利です。ユニコード文字列をUTF-8に分けて分割してくれます。(間違ったエンコーディングは1バイトを使って代わりにU+FFFDを出力します)


.. code-block:: cpp

   for pos, char := range "日本語" {
       fmt.Printf("character %c starts at byte position %d\n", char, pos)
   }

次を出力します。

.. code-block:: cpp

  character 日 starts at byte position 0
  character 本 starts at byte position 3
  character 語 starts at byte position 6


.. Finally, since Go has no comma operator and ++ and -- are statements not expressions, if you want to run multiple variables in a for you should use parallel assignment::

最後に、Goはカンマ演算子を持たず、++や--は文であり式ではないため、複数の変数をfor内で使用したい場合は並列代入を使わなければなりません。

..   // Reverse a

.. code-block:: cpp

  // aを逆転させる
  for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
      a[i], a[j] = a[j], a[i]
  }

Switch
------

.. Go's switch is more general than C's. The expressions need not be constants or even integers, the cases are evaluated top to bottom until a match is found, and if the switch has no expression it switches on true. It's therefore possible—and idiomatic—to write an if-else-if-else chain as a switch.

GoのswitchはCのものより雑多なものです。式は定数である必要はありませんし、整数ですらなくてもかまいません。caseは一致が見つかるまで上から順に評価されます。もしswitchが式を持っていなければ、真となるcaseに切り替えられます。よってswitchをif-else-if-elseのチェインをswitchで書くことが出来ますし、慣習的なものでもあります。

.. code-block:: cpp

  func unhex(c byte) byte {
      switch {
      case '0' <= c && c <= '9':
          return c - '0'
      case 'a' <= c && c <= 'f':
          return c - 'a' + 10
      case 'A' <= c && c <= 'F':
          return c - 'A' + 10
      }
      return 0
  }

.. There is no automatic fall through, but cases can be presented in comma-separated lists::

自動的にフォールスルーは発生しませんが、caseをカンマ区切りのリストで表現することが出来ます。

.. code-block:: cpp

  func shouldEscape(c byte) bool {
      switch c {
      case ' ', '?', '&', '=', '#', '+', '%':
          return true
      }
      return false
  }

.. Here's a comparison routine for byte arrays that uses two switch statements::

次に2つのswitch文を使ったバイト列の比較関数をお見せします。

.. code-block:: cpp

  // Compareは2つのバイト列を辞書順で比較した結果を整数として返します。
  // a == bの場合は0、a < bの場合は-1、a > bの場合は+1を返します。
  func Compare(a, b []byte) int {
      for i := 0; i < len(a) && i < len(b); i++ {
          switch {
          case a[i] > b[i]:
              return 1
          case a[i] < b[i]:
              return -1
          }
      }
      switch {
      case len(a) < len(b):
          return -1
      case len(a) > len(b):
          return 1
      }
      return 0
  }

.. // Compare returns an integer comparing the two byte arrays
.. // lexicographically.
.. // The result will be 0 if a == b, -1 if a < b, and +1 if a > b

.. A switch can also be used to discover the dynamic type of an interface variable. Such a type switch uses the syntax of a type assertion with the keyword type inside the parentheses. If the switch declares a variable in the expression, the variable will have the corresponding type in each clause::

switchはインタフェース変数の動的な型を調べる時にも使われます。こういったtype switchはtypeキーワードを使った型アサーションを括弧の中で使用します。switchが変数を式の中で宣言するとその変数はその節で対応する型として扱われます。

.. code-block:: cpp

  switch t := interfaceValue.(type) {
  default:
      fmt.Printf("unexpected type %T", type);  // %T prints type
  case bool:
      fmt.Printf("boolean %t\n", t);
  case int:
      fmt.Printf("integer %d\n", t);
  case *bool:
      fmt.Printf("pointer to boolean %t\n", *t);
  case *int:
      fmt.Printf("pointer to integer %d\n", *t);
  }

.. Functions

関数
=========

.. Multiple return values

複数の戻り値
----------------------

.. One of Go's unusual features is that functions and methods can return multiple values. 
.. This can be used to improve on a couple of clumsy idioms in C programs: in-band error returns (such as -1 for EOF) and modifying an argument.

Goでは、関数やメソッドは複数の値を返すことが出来ます。これは珍しい特徴ですが、C言語の(EOFをあらわす-1のような)in-bandエラーの戻り値や,
引数の変更といったような醜い構文を改善することが出来ます。


.. In C, a write error is signaled by a negative count with the error code secreted away in a volatile location.
.. In Go, Write can return a count and an error: “Yes, you wrote some bytes but not all of them because you filled the device”. 
.. The signature of *File.Write in package os is::

.. C言語では、書き込みエラーは、負の数値と非永続領域に隠蔽されたエラーコードによって通知されます。
C言語では、書き込みエラーは負の数値で通知され、エラーコードはどこかに隠されてしまいます。

G言語では、Writeオブジェクトは、数値と ”デバイスが一杯になったので、データの一部は書き込まれませんでした”というエラーを返します。
osパッケージの*File.Writeオブジェクトのシグネチャは以下のようになります。

.. code-block:: cpp

  func (file *File) Write(b []byte) (n int, err Error)

.. and as the documentation says, it returns the number of bytes written and a non-nil Error when n != len(b).
.. This is a common style; see the section on error handling for more examples.

ドキュメントによるとWriteは戻り値として、書き込まれたデータのバイト数と、
もし全てが書き込まれなかった場合(n != len(b)のとき)にはnilでないエラーを返すと書かれています。
これは共通のスタイルです。もしもっとたくさんの例を見たければ、エラーのセクションを参照してください。


.. A similar approach obviates the need to pass a pointer to a return value to simulate a reference parameter.
.. Here's a simple-minded function to grab a number from a position in a byte array, returning the number and the next position::

似たようなアプローチをとることで、参照変数をシミュレートするために戻り値にポインタを渡す必要がなくなります。
以下は、バイト配列の中から、指定した添え字の数値とその次の添え字を取り出す簡単な関数です。

.. code-block:: cpp

  func nextInt(b []byte, i int) (int, int) {
      for ; i < len(b) && !isDigit(b[i]); i++ {
      }
      x := 0;
      for ; i < len(b) && isDigit(b[i]); i++ {
          x = x*10 + int(b[i])-'0'
      }
      return x, i;
  }

.. You could use it to scan the numbers in an input array a like this::

これは次のように、入力した配列から数値を探し出すのに使うことができます。

.. code-block:: cpp

    for i := 0; i < len(a); {
        x, i = nextInt(a, i);
        fmt.Println(x);
    }

.. Named result parameters

名前付けされた戻り値
-----------------------

.. The return or result "parameters" of a Go function can be given names and used as regular variables, 
.. just like the incoming parameters. When named, they are

Goの関数の”戻り値”は、ちょうど入力値のように、名前をつけ普通の変数として扱うことが出来ます。

.. initialized to the zero values for their types when the function begins; 
.. if the function executes a return statement with no arguments, the current values of the 
.. result parameters are used as the returned values.

名前がつけられると、関数が始まるときにそれらの変数は、型に合った初期値で初期化されます。
もし、関数が実行された結果値を返さなかったら、その時点での変数の値が戻り値として返されます。

.. The names are not mandatory but they can make code shorter and clearer: they're documentation. 
.. If we name the results of nextInt it becomes obvious which returned int is which::

名前は必須ではありませんが、記述することでコードを短く、読みやすく出来ます。
ドキュメントには、nextIntの戻り値に名前をつけた例が載っています。


.. code-block:: cpp

  func nextInt(b []byte, pos int) (value, nextPos int) {

.. Because named results are initialized and tied to an unadorned return, they can simplify as well as clarify.
.. Here's a version of io.ReadFull that uses them well::

名前付けされた戻り値は初期化され、名前付けされていない変数と結び付けられます。
これらはとてもシンプルに記述できるだけでなく、分かりやすくすることができます。
以下は、io.ReadFullをこれらを上手く用いて書き直したものです。


.. code-block:: cpp

  func ReadFull(r Reader, buf []byte) (n int, err os.Error) {
      for len(buf) > 0 && err == nil {
          var nr int;
          nr, err = r.Read(buf);
          n += nr;
          buf = buf[nr:len(buf)];
      }
      return;
  }

.. Data
   ====

データ
======

.. Allocation with new()
   ---------------------

new()によるメモリ割り当て
-------------------------

.. Go has two allocation primitives, new() and make(). They do different things and apply to different types, which can be confusing, but the rules are simple. Let's talk about new() first. It's a built-in function essentially the same as its namesakes in other languages: new(T) allocates zeroed storage for a new item of type T and returns its address, a value of type \*T. In Go terminology, it returns a pointer to a newly allocated zero value of type T.

Goはメモリ割り当てプリミティブとしてnew()とmake()の２つを持っています。これらは動作も適用される型も異なっている為、混乱を招くかもしれませんがルールは単純です。まずはnew()から始めましょう。new()は組み込み関数で、他の言語での同名のものと本質的に同じです。new(T)は、T型のゼロで初期化された新しい要素を割り当て、そのアドレスを\*T型の値として返します。Goの用語では、new(T)は新しく確保されたT型のゼロ値へのポインタを返します。

.. Since the memory returned by new() is zeroed, it's helpful to arrange that the zeroed object can be used without further initialization. This means a user of the data structure can create one with new() and get right to work. For example, the documentation for bytes.Buffer states that "the zero value for Buffer is an empty buffer ready to use." Similarly, sync.Mutex does not have an explicit constructor or Init method. Instead, the zero value for a sync.Mutex is defined to be an unlocked mutex.

new()に返されたメモリはゼロクリアされているため、初期値がゼロで良いようなオブジェクトを使う場合には、特に初期化する事無く使う事ができて便利です。つまり、そのデータ構造は単にnew()するだけできちんと動作する、という事です。例えば、bytes.Bufferのドキュメントには、「ゼロの値をもつバッファは利用可能状態の空のバッファである」と定められています。同様にsync.Mutexも明示的なコンストラクタやInitメソッドを持ちません。代わりに、ゼロの値をもつsync.Mutexは非ロック状態のmutexであると定義されています。

.. The zero-value-is-useful property works transitively. Consider this type declaration::

「ゼロによる初期化は有用」という特徴はドミノ倒しに働きます。次の型宣言を考えてみましょう:

.. code-block:: cpp

  type SyncedBuffer struct {
      lock      sync.Mutex;
      buffer    bytes.Buffer;
  }

.. Values of type SyncedBuffer are also ready to use immediately upon allocation or just declaration. In this snippet, both p and v will work correctly without further arrangement::

SyncedBuffer型の値はnew()によるメモリ割り当てでも、単なる宣言であっても即使えるようになります。次のコード片では特に調整しなくてもp, v両方とも正しく動作します:

.. code-block:: cpp

  p := new(SyncedBuffer);  // type *SyncedBuffer
  var v SyncedBuffer;      // type  SyncedBuffer

.. Constructors and composite literals
   -----------------------------------

コンストラクタと複合リテラル
----------------------------

.. Sometimes the zero value isn't good enough and an initializing constructor is necessary, as in this example derived from package os::

時としてゼロ値だけでは不十分で、初期化コンストラクタが必要となる場合もあります。以下の例はosパッケージから派生した物です:

.. code-block:: cpp

  func NewFile(fd int, name string) *File {
      if fd < 0 {
          return nil
      }
      f := new(File);
      f.fd = fd;
      f.name = name;
      f.dirinfo = nil;
      f.nepipe = 0;
      return f;
  }

.. There's a lot of boiler plate in there. We can simplify it using a composite literal, which is an expression that creates a new instance each time it is evaluated::

上のコードはいささか冗長で、複合リテラルを使う事で簡単にできます。複合リテラルは評価されるたびに新しいインスタンスを生成する式です:

.. code-block:: cpp

  func NewFile(fd int, name string) *File {
      if fd < 0 {
          return nil
      }
      f := File{fd, name, nil, 0};
      return &f;
  }

.. Note that it's perfectly OK to return the address of a local variable; the storage associated with the variable survives after the function returns. In fact, taking the address of a composite literal allocates a fresh instance each time it is evaluated, so we can combine these last two lines::

ここで注意すべき点は、ローカル変数のアドレスを戻り値にするのは完全に合法であり、変数領域は関数が帰った後も保持される、ということです。実のところ、複合リテラルのアドレスを取得すると、その式が評価されるごとに新しいインスタンスが割り当てられるので、最後の2行は1行にまとめる事ができます:

.. code-block:: cpp

    return &File{fd, name, nil, 0};

.. The fields of a composite literal are laid out in order and must all be present. However, by labeling the elements explicitly as field:value pairs, the initializers can appear in any order, with the missing ones left as their respective zero values. Thus we could say::

複合リテラルのフィールドは定義順通りに、かつ漏れなく指定する必要があります。しかしながら、要素を明示的に field:value ペアのように書く事で任意の順序で書く事もできます。また指定しなかったフィールドはゼロに初期化されます。結果、以下のように書く事ができます:

.. code-block:: cpp

    return &File{fd: fd, name: name}

.. As a limiting case, if a composite literal contains no fields at all, it creates a zero value for the type. The expressions new(File) and &File{} are equivalent.

稀なケースとして、全くフィールドを含まない複合リテラルの場合、その型のゼロ値を生成します。new(File)と&File{}は等価です:

.. Composite literals can also be created for arrays, slices, and maps, with the field labels being indices or map keys as appropriate. In these examples, the initializations work regardless of the values of Enone, Eio, and Einval, as long as they are distinct::

複合リテラルは配列やスライス、マップの生成にも使えます。その場合フィールドにつけたラベルは、インデックスかマップのキーになります。以下の例では、Enone, Eio, Einvalが相異なってさえいれば、値に関係なく初期化は動作します:

.. code-block:: cpp

  a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"};
  s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"};
  m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"};

.. Allocation with make()
   ----------------------

make()によるメモリ割り当て
--------------------------

.. Back to allocation. The built-in function make(T, args) serves a purpose different from new(T). It creates slices, maps, and channels only, and it returns an initialized (not zero) value of type T, not \*T. The reason for the distinction is that these three types are, under the covers, references to data structures that must be initialized before use. A slice, for example, is a three-item descriptor containing a pointer to the data (inside an array), the length, and the capacity; until those items are initialized, the slice is nil. For slices, maps, and channels, make initializes the internal data structure and prepares the value for use. For instance::

メモリ割り当てに戻りましょう。組み込み関数make(T, args)はnew(T)とは違った目的に使われます。make()はスライス、マップ、チャンネル専用で、初期化（ゼロではありません）したT型(\*T型ではありません）を返します。この区別をしている理由は、内部的にはこれら３つのタイプが、使用前に初期化が必要なデータ構造への参照となっているためです。例えばスライスは３要素の記述子で（配列内の）データへのポインタ、長さ、容量を含んでいます。これらが初期化されるまで、スライスはnilです。スライス、マップ、チャンネルでは make は内部構造を初期化して、使用する値を作成します。たとえば:

.. code-block:: cpp

  make([]int, 10, 100)

.. allocates an array of 100 ints and then creates a slice structure with length 10 and a capacity of 100 pointing at the first 10 elements of the array. (When making a slice, the capacity can be omitted; see the section on slices for more information.) In contrast, new([]int) returns a pointer to a newly allocated, zeroed slice structure, that is, a pointer to a nil slice value.

上記コードは100個のint配列を割り当て、その後、スライスの構造を長さ10で容量100で作成します(スライスを作る際、容量は無視されることがあります。詳細はスライスの節を参照してください)。対照的に new([]int)は新規に割り当てたゼロ値のスライス構造へのポインタ、すなわちnilスライスへのポインタを返します。

.. These examples illustrate the difference between new() and make()::

次の例はnew(), make()の違いを示しています。

.. code-block:: cpp

  var p *[]int = new([]int);       // allocates slice structure; *p == nil; rarely useful
  var v  []int = make([]int, 100); // v now refers to a new array of 100 ints
  
  // Unnecessarily complex:
  var p *[]int = new([]int);
  *p = make([]int, 100, 100);
  
  // Idiomatic:
  v := make([]int, 100);

.. Remember that make() applies only to maps, slices and channels and does not return a pointer. To obtain an explicit pointer allocate with new().

make()はマップ、スライス、チャネルのいずれかのみに適用でき、ポインタを返さないことに注意してください。明示的にポインタを取得するにはnew()で割り当てます。

.. Arrays
   ------

配列
----

.. Arrays are useful when planning the detailed layout of memory and sometimes can help avoid allocation, but primarily they are a building block for slices, the subject of the next section. To lay the foundation for that topic, here are a few words about arrays.

配列は、メモリレイアウトの詳細が分かっている場合に有用で、時に割り当てを避けるのに役に立つ事がありますが、もっぱら次の節の題目である、スライスの素材として使われます。そのトピックの基礎を築くために、いくつかの配列についての言明があります。

.. There are major differences between the ways arrays work in Go and C. In Go,

配列どう動作するか、の点でGoとCとでは大きな違いがあります。Goでは、

.. * Arrays are values. Assigning one array to another copies all the elements.
   * In particular, if you pass an array to a function, it will receive a copy of the array, not a pointer to it.
   * The size of an array is part of its type. The types [10]int and [20]int are distinct.

* 配列は値です。ある配列を別の配列に代入することは全要素のコピーになります。
* 特に、配列を関数に渡す場合、ポインタではなく配列のコピーを受け取る事になります。
* 配列のサイズは型の一部です。[10]int と [20]int は異なる型となります。

.. The value property can be useful but also expensive; if you want C-like behavior and efficiency, you can pass a pointer to the array::

配列が値である、という特性は便利ですが同時に効率が悪くなります。Cのような振る舞いと効率を求めるなら、配列のポインタを渡す事もできます。

.. code-block:: cpp

  func Sum(a *[3]float) (sum float) {
      for _, v := range a {
          sum += v
      }
      return
  }

  array := [...]float{7.0, 8.5, 9.1};
  x := sum(&array);  // Note the explicit address-of operator

.. But even this style isn't idiomatic Go. Slices are.

しかし、この書き方もまたGoらしいスタイルではありません。Goらしいと言えばスライスです。

.. Slices
   ------

スライス
--------

.. Slices wrap arrays to give a more general, powerful, and convenient interface to sequences of data. Except for items with explicit dimension such as transformation matrices, most array programming in Go is done with slices rather than simple arrays.

スライスは配列をラップし、連続データへの汎用的・強力かつ便利なインターフェイスを提供します。変換行列のように明示的な次元を持つものを除き、Goでは殆どの配列プログラミングが、単純な配列よりむしろスライスを使って行われます。

.. Slices are reference types, which means that if you assign one slice to another, both refer to the same underlying array. For instance, if a function takes a slice argument, changes it makes to the elements of the slice will be visible to the caller, analogous to passing a pointer to the underlying array. A Read function can therefore accept a slice argument rather than a pointer and a count; the length within the slice sets an upper limit of how much data to read. Here is the signature of the Read method of the File type in package os::

スライスは参照型、つまりスライスに別のスライスを代入した場合、双方のスライスは同じ元の配列を指します。例えば、スライスを引数にとる関数の場合、その関数がスライスの要素に行った変更は呼び出し元(caller)にも見えます。これは元の配列のポインタを渡すのと似ています。Read関数は従って、ポインタと要素数を受け取るのではなく、スライスを引数として受ける事ができます。スライス内部の長さはデータ数の上限値に設定されます。次の行はosパッケージ：File型のReadメソッドのシグネチャです。

.. code-block:: cpp

    func (file *File) Read(buf []byte) (n int, err os.Error)

.. The method returns the number of bytes read and an error value, if any. To read into the first 32 bytes of a larger buffer b, slice (here used as a verb) the buffer::

このメソッドはリードしたバイト数および（もしあれば）エラー値を返します。大きなバッファbから最初の32バイトを読むには以下のようにバッファをスライスします。

.. code-block:: cpp

    n, err := f.Read(buf[0:32]);

.. Such slicing is common and efficient. In fact, leaving efficiency aside for the moment, this snippet would also read the first 32 bytes of the buffer::

このようなスライスは一般的で効率が良いです。実際、効率を無視すれば、次のようなコードでも同じ事が可能です。

.. code-block:: cpp

    var n int;
    var err os.Error;
    for i := 0; i < 32; i++ {
        nbytes, e := f.Read(buf[i:i+1]);  // Read one byte.
        if nbytes == 0 || e != nil {
            err = e;
            break;
        }
        n += nbytes;
    }

.. The length of a slice may be changed as long as it still fits within the limits of the underlying array; just assign it to a slice of itself. The capacity of a slice, accessible by the built-in function cap, reports the maximum length the slice may assume. Here is a function to append data to a slice. If the data exceeds the capacity, the slice is reallocated. The resulting slice is returned. The function uses the fact that len and cap are legal when applied to the nil slice, and return 0::

スライスの長さは元の配列の大きさに収まっている限り、単にスライスに代入するだけで自由に変更できます。スライスの容量は、組み込み関数capにてアクセスする事ができます。スライスが仮定している最大の長さをレポートします。スライスにデータを追加する関数です。容量を超えた場合、スライスは再割り当てされます。結果のスライスが戻ります。この関数はlen, capは nilスライスに適用した場合でも 0を返す、ということを利用しています。

.. code-block:: cpp

  func Append(slice, data[]byte) []byte {
      l := len(slice);
      if l + len(data) > cap(slice) {    // reallocate
          // Allocate double what's needed, for future growth.
          newSlice := make([]byte, (l+len(data))*2);
          // Copy data (could use bytes.Copy()).
          for i, c := range slice {
              newSlice[i] = c
          }
          slice = newSlice;
      }
      slice = slice[0:l+len(data)];
      for i, c := range data {
          slice[l+i] = c
      }
      return slice;
  }

.. We must return the slice afterwards because, although Append can modify the elements of slice, the slice itself (the run-time data structure holding the pointer, length, and capacity) is passed by value.

.. FIXME

スライスをあとから戻すべきです。なぜなら、Appendはスライスの要素を変更するかもしれませんが、スライス自身（ポインタ、長さ、容量を持った実行時データ構造）が値として渡されるからです。

.. Maps
   ----

マップ
------

.. Maps are a convenient and powerful built-in data structure to associate values of different types. The key can be of any type for which the equality operator is defined, such as integers, floats, strings, pointers, and interfaces (as long as the dynamic type supports equality). Structs, arrays and slices cannot be used as map keys, because equality is not defined on those types. Like slices, maps are a reference type. If you pass a map to a function that changes the contents of the map, the changes will be visible in the caller.

マップは値を別の型に関連づける、便利で強力な組み込みデータ構造です。キーには整数や浮動小数点数、文字列、ポインタ、インターフェイス（動的型が同値をサポートする限り）のように同値演算子が定義されていればどんな型でも使えます。構造体、配列、またスライスは同値が定義されていないので、マップのキーとして使えません。スライスのようにマップは参照型です。そのマップ内部を変更する関数にマップを渡す場合、呼び出し側にも変更は見えます。

.. Maps can be constructed using the usual composite literal syntax with colon-separated key-value pairs, so it's easy to build them during initialization::

マップはコロンで分割したkey-valueペアをつかって普通の複合リテラル文法で構築する事ができます。初期化のときにつくるのも簡単。

.. code-block:: cpp

  var timeZone = map[string] int {
      "UTC":  0*60*60,
      "EST": -5*60*60,
      "CST": -6*60*60,
      "MST": -7*60*60,
      "PST": -8*60*60,
  }

.. Assigning and fetching map values looks syntactically just like doing the same for arrays except that the index doesn't need to be an integer. An attempt to fetch a map value with a key that is not present in the map will cause the program to crash, but there is a way to do so safely using a multiple assignment::

マップの代入と読み出しはインデックスが整数でなくても良いという部分を除くとほぼ配列と同じです。存在しないキーを指定した場合、プログラムはクラッシュします。しかし、並列代入によってこれを安全に行う方法があります。

.. code-block:: cpp

  var seconds int;
  var ok bool;
  seconds, ok = timeZone[tz]

.. For obvious reasons this is called the “comma ok” idiom. In this example, if tz is present, seconds will be set appropriately and ok will be true; if not, seconds will be set to zero and ok will be false. Here's a function that puts it together::

これは、自明な理由から"comma ok"イディオムと呼ばれます。この例ではtzが存在していればsecondsには該当する値が設定されokにはtrueが、でなければsecondsにはゼロが設定され okには falseが入ります。以下は両者を出力する関数です:

.. code-block:: cpp

  func offset(tz string) int {
      if seconds, ok := timeZone[tz]; ok {
          return seconds
      }
      log.Stderr("unknown time zone", tz);
      return 0;
  }

.. To test for presence in the map without worrying about the actual value, you can use the blank identifier, a simple underscore (_). The blank identifier can be assigned or declared with any value of any type, with the value discarded harmlessly. For testing presence in a map, use the blank identifier in place of the usual variable for the value::

値を読まずにマップ中の存在をチェックするには、空の識別子、単にアンダースコア(_)、を使います。空の識別子はあらゆる値、あらゆる型を代入/宣言する事ができ、値は安全に破棄する事ができます。マップ中での存在をテストには、空の識別子を値の普通の変数のところに配置します。

.. code-block:: cpp

_, present := timeZone[tz];

.. To delete a map entry, turn the multiple assignment around by placing an extra boolean on the right; if the boolean is false, the entry is deleted. It's safe to do this even if the key is already absent from the map::

マップのエントリを削除するには、並列代入をひっくり返して、追加の論理値を右に書きます。論理値がfalseならエントリは削除されます。キーが既にマップから削除済みであっても安全に行う事ができます。

.. code-block:: cpp

  timeZone["PDT"] = 0, false;  // Now on Standard Time

.. Printing
   --------

印字
----

.. Formatted printing in Go uses a style similar to C's printf family but is richer and more general. The functions live in the fmt package and have capitalized names: fmt.Printf, fmt.Fprintf, fmt.Sprintf and so on. The string functions (Sprintf etc.) return a string rather than filling in a provided buffer.

Goの書式付き表示はCのprintfファミリーと似ていますがより機能豊富で一般的です。それらの関数はfmtパッケージの内部にあり、先頭大文字の名前: fmt.Printf, fmt.Sprintf, などとなっています。文字列関数(Sprintfなど)は指定されたバッファに放り込むのではなく、文字列を返します。

.. You don't need to provide a format string. For each of Printf, Fprintf and Sprintf there is another pair of functions, for instance Print and Println. These functions do not take a format string but instead generate a default format for each argument. The ln version also inserts a blank between arguments if neither is a string and appends a newline to the output. In this example each line produces the same output::

書式文字列を指定する必要はありません。Printf, FPrintf, SprintfはそれぞれPrint, Printlnのペアを持っている。これらの関数は書式文字列をとらず、各引数のデフォルトの書式を生成する。lnバージョンは更に文字列でなければ各引数の間にスペースを挿入し、行の最後に改行を追加する

.. code-block:: cpp

  fmt.Printf("Hello %d\n", 23);
  fmt.Fprint(os.Stdout, "Hello ", 23, "\n");
  fmt.Println(fmt.Sprint("Hello ", 23));

.. As mentioned in the tutorial, fmt.Fprint and friends take as a first argument any object that implements the io.Writer interface; the variables os.Stdout and os.Stderr are familiar instances.

チュートリアルでも触れた通り、fmt.Fprintfとその仲間は第一引数として io.Writer インターフェイスを実装した任意のオブジェクトをとります。変数 os.Stdout, os.Stderr は見慣れた例です。

.. Here things start to diverge from C. First, the numeric formats such as %d do not take flags for signedness or size; instead, the printing routines use the type of the argument to decide these properties::

このあたりからCとの違いが出てきます。第一に%dなどの数値書式は符号やサイズなどのフラグを受け付けません。代わりに表示ルーチンは引数の型からこれらの性質を決定します。

.. code-block:: cpp

  var x uint64 = 1<<64 - 1;
  fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x));

.. prints:

表示はこうなります::

  18446744073709551615 ffffffffffffffff; -1 -1

.. If you just want the default conversion, such as decimal for integers, you can use the catchall format %v (for “value”); the result is exactly what Print and Println would produce. Moreover, that format can print any value, even arrays, structs, and maps. Here is a print statement for the time zone map defined in the previous section::

整数の場合の十進表示のようにデフォルトの変換だけでよければ、なんでもあり書式 %v (値のv)を使う事ができます。結果はPrintやPrintlnの出力と全く同じになります。さらにこの書式は任意の値、配列や構造体、マップであっても表示できます。次の例は前節でのタイムゾーンマップを表示する文です:

.. code-block:: cpp

  fmt.Printf("%v\n", timeZone);  // or just fmt.Println(timeZone);

.. which gives output::

以下の出力を得ます::

  map[CST:-21600 PST:-28800 EST:-18000 UTC:0 MST:-25200]

.. For maps the keys may be output in any order, of course. When printing a struct, the modified format %+v annotates the fields of the structure with their names, and for any value the alternate format %#v prints the value in full Go syntax::

マップの場合、キーの順序はもちろん任意です。構造体を表示する場合、変更書式 %+v は構造体のフィールドを名前付きで表示するように指示します。代替書式として %#vがあり、これは Goの完全な文法を表示します。

.. code-block:: cpp

  type T struct {
      a int;
      b float;
      c string;
  }
  t := &T{ 7, -2.35, "abc\tdef" };
  fmt.Printf("%v\n", t);
  fmt.Printf("%+v\n", t);
  fmt.Printf("%#v\n", t);
  fmt.Printf("%#v\n", timeZone);

.. prints :

表示は以下の通り::

  &{7 -2.35 abc   def}
  &{a:7 b:-2.35 c:abc     def}
  &main.T{a:7, b:-2.35, c:"abc\tdef"}
  map[string] int{"CST":-21600, "PST":-28800, "EST":-18000, "UTC":0, "MST":-25200}
  
.. (Note the ampersands.) That quoted string format is also available through %q when applied to a value of type string or []byte; the alternate format %#q will use backquotes instead if possible. Also, %x works on strings and arrays of bytes as well as on integers, generating a long hexadecimal string, and with a space in the format (% x) it puts spaces between the bytes.

(アンパサンド&に注意) クォートされたこれらの文字列書式は、対象が文字列型の値か[]byte型の場合 %qでも得る事ができます。別の書式 %#qは可能であればバッククォートをつけます。更に, %xは文字列とバイトおよびint配列で動作し、16進数の長い文字列を生成します。スペース付き書式% xだとバイト区切りにスペースを追加します。

.. Another handy format is %T, which prints the type of a value::

もう一つの便利な書式は %T です。これは値の型を出力します。

  fmt.Printf("%T\n", timeZone);

.. prints :

表示は以下の通り::

  map[string] int

.. If you want to control the default format for a custom type, all that's required is to define a method String() string on the type. For our simple type T, that might look like this::

もし、カスタム型について、デフォルトの書式を変更したければ、必要なのはその型についてString()メソッドを定義することです。単純な型 Tについて、以下のようになります。

.. code-block:: cpp

  func (t *T) String() string {
      return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c);
  }
  fmt.Printf("%v\n", t);

.. to print in the format::

書式付きで表示すると以下のようになります::

  7/-2.35/"abc\tdef"

.. Our String() method is able to call Sprintf because the print routines are fully reentrant and can be used recursively. We can even go one step further and pass a print routine's arguments directly to another such routine. The signature of Printf uses the ... type for its final argument to specify that an arbitrary number of parameters can appear after the format::

printルーチンは完全に再入可能に書かれているので再帰的に利用する事ができます。結果、String()メソッドはSprintfを呼び出す事が可能です。更に進んで表示ルーチンの引数を直接他のそういったルーチンに渡す事すらできます。Printfのシグネチャの最後の引数に ... 型の使う事で、任意の数のパラメータを書式の後に書く事ができる事を示します。

.. code-block:: cpp

  func Printf(format string, v ...) (n int, errno os.Error) {

.. Within the function Printf, v is a variable that can be passed, for instance, to another print routine. Here is the implementation of the function log.Stderr we used above. It passes its arguments directly to fmt.Sprintln for the actual formatting::

関数 Printfの中で、v は渡す事のできる変数で、例えば、他の表示ルーチンです。次の例は上で使った log.Stderr 関数の実装です。これはその引数を fmt.Sprintlnに直接実際の書式を送っています。

.. code-block:: cpp

  // Stderr is a helper function for easy logging to stderr. It is analogous to Fprint(os.Stderr).
  func Stderr(v ...) {
      stderr.Output(2, fmt.Sprintln(v));  // Output takes parameters (int, string)
  }

.. There's even more to printing than we've covered here. See the godoc documentation for package fmt for the details.

表示についてここで全てを網羅することはできません。詳細についてはfmtパッケージのgodocドキュメントを参照してください。

.. Initialization
   ==============

初期化
======

.. Although it doesn't look superficially very different from initialization in C or C++, initialization in Go is more powerful. Complex structures can be built during initialization and the ordering issues between initialized objects in different packages are handled correctly.

Goの初期化は一見CやC++と大して変わらないように見えますが、より強力なものです。複雑な構造を初期化中に構築可能で、異なるパッケージ中のオブジェクト間の初期化順序も正しく処理します。

.. Constants
   ---------

定数
----

.. Constants in Go are just that?constant. They are created at compile time, even when defined as locals in functions, and can only be numbers, strings or booleans. Because of the compile-time restriction, the expressions that define them must be constant expressions, evaluatable by the compiler. For instance, 1<<3 is a constant expression, while math.Sin(math.Pi/4) is not because the function call to math.Sin needs to happen at run time.

Goの定数は文字通り定数です。定数は、たとえ関数でローカル定義されていてもコンパイル時に生成されます。また定数は、数値、文字列、論理値のいずれかでなくてはいけません。コンパイル時という制約から、それらを定義する式はコンパイラが評価できる定数式である必要があります。例えば、 1<<3 は定数式ですが、math.Sin(math.Pi/4)はmath.Sinへの関数呼び出しが実行時に発生する為、定数式ではありません。

.. In Go, enumerated constants are created using the iota enumerator. Since iota can be part of an expression and expressions can be implicitly repeated, it is easy to build intricate sets of values::

Goでは列挙定数はiota列挙子をつかって生成されます。iotaは式の一部にもでき、式は暗黙的に繰り返されるので、複雑な値の集合も簡単に生成できます:

.. code-block:: cpp

  type ByteSize float64
  const (
      _ = iota;    // 空の識別子に割り当てて、最初の値（０）を無視する
      KB ByteSize = 1<<(10*iota);
      MB;
      GB;
      TB;
      PB;
      YB;
  )

.. type ByteSize float64
  const (
      _ = iota;    // ignore first value by assigning to blank identifier
      KB ByteSize = 1<<(10*iota);
      MB;
      GB;
      TB;
      PB;
      YB;
  )

.. The ability to attach a method such as String to a type makes it possible for such values to format themselves automatically for printing, even as part of a general type::

型にString()などのメソッドを関連づける事で、丁度普通の組み込み型のように自動的にフォーマット表示させる事が可能です:

.. code-block:: cpp

  func (b ByteSize) String() string {
      switch {
      case s >= YB:
          return fmt.Sprintf("%.2fYB", b/YB)
      case s >= PB:
          return fmt.Sprintf("%.2fPB", b/PB)
      case s >= TB:
          return fmt.Sprintf("%.2fTB", b/TB)
      case s >= GB:
          return fmt.Sprintf("%.2fGB", b/GB)
      case s >= MB:
          return fmt.Sprintf("%.2fMB", b/MB)
      case s >= KB:
          return fmt.Sprintf("%.2fKB", b/KB)
      }
      return fmt.Sprintf("%.2fB", b)
  }

.. The expression YB prints as 1.00YB, while ByteSize(1e13) prints as 9.09TB,

式YBは1.00YBと表示され、ByteSize(1e13)は9.09TBと表示されます。

.. Variables
   ---------

変数
---------

.. Variables can be initialized just like constants but the initializer can be a general expression computed at run time::

変数は定数と同じように初期化することも可能ですが、初期化子に実行時に計算される普通の式が使えます:

.. code-block:: cpp

  var (
      HOME = os.Getenv("HOME");
      USER = os.Getenv("USER");
      GOROOT = os.Getenv("GOROOT");
  )

.. The init function
   -----------------

init 関数
---------

.. Finally, each source file can define its own init() function to set up whatever state is required. The only restriction is that, although goroutines can be launched during initialization, they will not begin execution until it completes; initialization always runs as a single thread of execution. And finally means finally: init() is called after all the variable declarations in the package have evaluated their initializers, and those are evaluated only after all the imported packages have been initialized.

最後に、各ソースファイルは必要なあらゆる状態を設定するために、独自のinit()関数を定義する事ができます。唯一の制約は、初期化中にgoroutineを起動する事はできますが、初期化が完了するまで実行されないという事です。つまり初期化は常にシングルスレッドとして実行されます。また「最後に」と言うのはまさしく「最後」で、init()が呼び出されるのはパッケージ中の全ての変数宣言が初期化子を評価し、全てのimportされているパッケージが初期化された後です。

.. Besides initializations that cannot be expressed as declarations, a common use of init() functions is to verify or repair correctness of the program state before real execution begins::

宣言として表現できない初期化の他に、init()関数の良く使われる用法の一つとしては実際の実行が始まる前にプログラム状態の正当性を検証したり修正することがあります。:

.. code-block:: cpp

  func init() {
      if USER == "" {
          log.Exit("$USER not set")
      }
      if HOME == "" {
          HOME = "/usr/" + USER
      }
      if GOROOT == "" {
          GOROOT = HOME + "/go"
      }
      // GOROOT はコマンドラインの--gorootフラグで上書き可能
      flag.StringVar(&GOROOT, "goroot", GOROOT, "Go root directory")
  }

.. func init() {
      if USER == "" {
          log.Exit("$USER not set")
      }
      if HOME == "" {
          HOME = "/usr/" + USER
      }
      if GOROOT == "" {
          GOROOT = HOME + "/go"
      }
      // GOROOT may be overridden by --goroot flag on command line.
      flag.StringVar(&GOROOT, "goroot", GOROOT, "Go root directory")
  }

.. Methods

関数
=======

.. Pointers vs. Values

ポインタ vs 値
-------------------

.. Methods can be defined for any named type that is not a pointer or an interface; the receiver does not have to be a struct.

関数は、ポインタ型・インターフェースでない任意の型で定義することができます。受け側は、構造体でなければ定義する必要はありません。

.. In the discussion of slices above, we wrote an Append function. We can define it as a method on slices instead. 

.. To do this, we first declare a named type to which we can bind the method, and then make the receiver for the method a value of that type::

以下のサンプルは、スライスに追加する関数です。 我々のスライス上のメソッドの代わりとして定義することができます。
これを行うには、まず名前付き型を宣言します、これを関数に結びつけます。受け側は、関数の返り値の型を定義します。

.. type ByteSlice []byte
  
  func (slice ByteSlice) Append(data []byte) []slice {
      // Body exactly the same as above
  }

.. code-block:: cpp
  
  type ByteSlice []byte
  
  func (slice ByteSlice) Append(data []byte) []slice {
      // 関数内の処理は、上記と同じ
  }


.. This still requires the method to return the updated slice.  We can eliminate that clumsiness by redefining the method to take a pointer to a ByteSlice as its receiver, so the method can overwrite the caller's slice::

まだ関数には、更新したスライスを返す処理が必要です。
メソッドの再定義をすることで、受信側はByteSliceへのポインタを取ることができ、ぎこちなさを排除することができます
この関数は、呼び出し元スライスを上書きすることができます。

..  func (p *ByteSlice) Append(data []byte) {
      slice := *p;
      // Body as above, without the return.
      *p = slice;
  }

.. code-block:: cpp

  func (p *ByteSlice) Append(data []byte) {
      slice := *p;
      // 返り値は省略しています。
      *p = slice;
  }

.. In fact, we can do even better. If we modify our function so it looks like a standard Write method, like this::

実際、我々もよく使います。 もし、標準的なWriteメソッドを修正する場合は、

..  func (p *ByteSlice) Write(data []byte) (n int, err os.Error) {
      slice := *p;
      // Again as above.
      *p = slice;
      return len(data), nil)
  }

.. code-block:: cpp

  func (p *ByteSlice) Write(data []byte) (n int, err os.Error) {
      slice := *p;
      // 上記から
      *p = slice;
      return len(data), nil)
  }

.. then the type \*ByteSlice satisfies the standard interface io.Writer, which is handy. For instance, we can print into one::

\*ByteSlice型を満たす標準的なインターフェースでは、io.Writerが便利です。
例えば、スライスを一つとして表示することができます。

..  var b ByteSlice;
    fmt.Fprintf(&b, "This hour has %d days\n", 7);

.. code-block:: cpp

    var b ByteSlice;
    fmt.Fprintf(&b, "This hour has %d days\n", 7);

.. We pass the address of a ByteSlice because only \*ByteSlice satisfies io.Writer. 

.. The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers. 

.. This is because pointer methods can modify the receiver; invoking them on a copy of the value would cause those modifications to be discarded.

io.Writerの*ByteSliceを渡す要求を満たすために、ByteSliceを渡す必要があります。
受け側で、ポインタ型と値のどちらを使うかというと、値を受け取る関数は、ポインタ型と値を受け取りますが、ポインタ関数は、ポインタのみ受け取ります。
ポインタ関数では、受け側を修正することができます。理由は、値のコピーによる変更の破棄を引き起こす恐れがあるためです。

.. By the way, the idea of using Write on a slice of bytes is implemented by bytes.Buffer.

ところで、bytes.Bufferは、このアイデアを使って、バイト単位でのスライス書き込みを実装しました。

Interfaces and other types
==========================

Interfaces
----------

Interfaces in Go provide a way to specify the behavior of an object: if something can do this, then it can be used here. We've seen a couple of simple examples already; custom printers can be implemented by a String method while Fprintf can generate output to anything with a Write method. Interfaces with only one or two methods are common in Go code, and are usually given a name derived from the method, such as io.Writer for something that implements Write.

A type can implement multiple interfaces. For instance, a collection can be sorted by the routines in package sort if it implements sort.Interface, which contains Len(), Less(i, j int) bool, and Swap(i, j int), and it could also have a custom formatter. In this contrived example Sequence satisfies both::

  type Sequence []int
  
  // Methods required by sort.Interface.
  func (s Sequence) Len() int {
      return len(s)
  }
  func (s Sequence) Less(i, j int) bool {
      return s[i] < s[j]
  }
  func (s Sequence) Swap(i, j int) {
      s[i], s[j] = s[j], s[i]
  }
  
  // Method for printing - sorts the elements before printing.
  func (s Sequence) String() string {
      sort.Sort(s);
      str := "[";
      for i, elem := range s {
          if i > 0 {
              str += " "
          }
          str += fmt.Sprint(elem);
      }
      return str + "]";
  }

Conversions
-----------

The String method of Sequence is recreating the work that Sprint already does for slices. We can share the effort if we convert the Sequence to a plain []int before calling Sprint::

  func (s Sequence) String() string {
      sort.Sort(s);
      return fmt.Sprint([]int(s));
  }

The conversion causes s to be treated as an ordinary slice and therefore receive the default formatting. Without the conversion, Sprint would find the String method of Sequence and recur indefinitely. Because the two types (Sequence and []int) are the same if we ignore the type name, it's legal to convert between them. The conversion doesn't create a new value, it just temporarily acts as though the existing value has a new type. (There are other legal conversions, such as from integer to float, that do create a new value.)

It's an idiom in Go programs to convert the type of an expression to access a different set of methods. As an example, we could use the existing type sort.IntArray to reduce the entire example to this::

  type Sequence []int
  
  // Method for printing - sorts the elements before printing
  func (s Sequence) String() string {
      sort.IntArray(s).Sort();
      return fmt.Sprint([]int(s))
  }

Now, instead of having Sequence implement multiple interfaces (sorting and printing), we're using the ability of a data item to be converted to multiple types (Sequence, sort.IntArray and []int), each of which does some part of the job. That's more unusual in practice but can be effective.

Generality
----------

If a type exists only to implement an interface and has no exported methods beyond that interface, there is no need to export the type itself. Exporting just the interface makes it clear that it's the behavior that matters, not the implementation, and that other implementations with different properties can mirror the behavior of the original type. It also avoids the need to repeat the documentation on every instance of a common method.

In such cases, the constructor should return an interface value rather than the implementing type. As an example, in the hash libraries both crc32.NewIEEE() and adler32.New() return the interface type hash.Hash32. Substituting the CRC-32 algorithm for Adler-32 in a Go program requires only changing the constructor call; the rest of the code is unaffected by the change of algorithm.

A similar approach allows the streaming cipher algorithms in the crypto/block package to be separated from the block ciphers they chain together. By analogy with the bufio package, they wrap a Cipher interface and return hash.Hash, io.Reader, or io.Writer interface values, not specific implementations.

The interface to crypto/block includes::

  type Cipher interface {
      BlockSize() int;
      Encrypt(src, dst []byte);
      Decrypt(src, dst []byte);
  }
  
  // NewECBDecrypter returns a reader that reads data
  // from r and decrypts it using c in electronic codebook (ECB) mode.
  func NewECBDecrypter(c Cipher, r io.Reader) io.Reader
  
  // NewCBCDecrypter returns a reader that reads data
  // from r and decrypts it using c in cipher block chaining (CBC) mode
  // with the initialization vector iv.
  func NewCBCDecrypter(c Cipher, iv []byte, r io.Reader) io.Reader

NewECBDecrypter and NewCBCReader apply not just to one specific encryption algorithm and data source but to any implementation of the Cipher interface and any io.Reader. Because they return io.Reader interface values, replacing ECB encryption with CBC encryption is a localized change. The constructor calls must be edited, but because the surrounding code must treat the result only as an io.Reader, it won't notice the difference.

Interfaces and methods
----------------------

Since almost anything can have methods attached, almost anything can satisfy an interface. One illustrative example is in the http package, which defines the Handler interface. Any object that implements Handler can serve HTTP requests::

  type Handler interface {
      ServeHTTP(*Conn, *Request);
  }

For brevity, let's ignore POSTs and assume HTTP requests are always GETs; that simplification does not affect the way the handlers are set up. Here's a trivial but complete implementation of a handler to count the number of times the page is visited::

  // Simple counter server.
  type Counter struct {
      n int;
  }
  
  func (ctr *Counter) ServeHTTP(c *http.Conn, req *http.Request) {
      ctr.n++;
      fmt.Fprintf(c, "counter = %d\n", ctr.n);
  }

(Keeping with our theme, note how Fprintf can print to an HTTP connection.) For reference, here's how to attach such a server to a node on the URL tree::

  import "http"
  ...
  ctr := new(Counter);
  http.Handle("/counter", ctr);

But why make Counter a struct? An integer is all that's needed. (The receiver needs to be a pointer so the increment is visible to the caller.)::

  // Simpler counter server.
  type Counter int
  
  func (ctr *Counter) ServeHTTP(c *http.Conn, req *http.Request) {
      *ctr++;
      fmt.Fprintf(c, "counter = %d\n", *ctr);
  }

What if your program has some internal state that needs to be notified that a page has been visited? Tie a channel to the web page::

  // A channel that sends a notification on each visit.
  // (Probably want the channel to be buffered.)
  type Chan chan *http.Request
  
  func (ch Chan) ServeHTTP(c *http.Conn, req *http.Request) {
      ch <- req;
      fmt.Fprint(c, "notification sent");
  }

Finally, let's say we wanted to present on /args the arguments used when invoking the server binary. It's easy to write a function to print the arguments::

  func ArgServer() {
      for i, s := range os.Args {
          fmt.Println(s);
      }
  }

How do we turn that into an HTTP server? We could make ArgServer a method of some type whose value we ignore, but there's a cleaner way. Since we can define a method for any type except pointers and interfaces, we can write a method for a function. The http package contains this code::

  // The HandlerFunc type is an adapter to allow the use of
  // ordinary functions as HTTP handlers.  If f is a function
  // with the appropriate signature, HandlerFunc(f) is a
  // Handler object that calls f.
  type HandlerFunc func(*Conn, *Request)
  
  // ServeHTTP calls f(c, req).
  func (f HandlerFunc) ServeHTTP(c *Conn, req *Request) {
      f(c, req);
  }

HandlerFunc is a type with a method, ServeHTTP, so values of that type can serve HTTP requests. Look at the implementation of the method: the receiver is a function, f, and the method calls f. That may seem odd but it's not that different from, say, the receiver being a channel and the method sending on the channel.

To make ArgServer into an HTTP server, we first modify it to have the right signature::

  // Argument server.
  func ArgServer(c *http.Conn, req *http.Request) {
      for i, s := range os.Args {
          fmt.Fprintln(c, s);
      }
  }

ArgServer now has same signature as HandlerFunc, so it can be converted to that type to access its methods, just as we converted Sequence to IntArray to access IntArray.Sort. The code to set it up is concise::

  http.Handle("/args", http.HandlerFunc(ArgServer));

When someone visits the page /args, the handler installed at that page has value ArgServer and type HandlerFunc. The HTTP server will invoke the method ServeHTTP of that type, with ArgServer as the receiver, which will in turn call ArgServer (via the invocation f(c, req) inside HandlerFunc.ServeHTTP). The arguments will then be displayed.

In this section we have made an HTTP server from a struct, an integer, a channel, and a function, all because interfaces are just sets of methods, which can be defined for (almost) any type.

.. Embedding
   =========

埋め込み
========

.. Go does not provide the typical, type-driven notion of subclassing, but it does have the ability to “borrow” pieces of an implementation by embedding types within a struct or interface.

Goはよくみかける型駆動のサブクラス定義方法を提供しませんが、型をstructやinterfaceの中に埋め込むことによって実装の一部を"借りる"機能を持っています。

.. Interface embedding is very simple. We've mentioned the io.Reader and io.Writer interfaces before; here are their definitions:

インターフェースの埋め込みは非常にシンプルです。io.Readerとio.Writerインターフェースについては前にふれました。これらの定義は次のとおりです。

.. code-block:: cpp

  type Reader interface {
      Read(p []byte) (n int, err os.Error);
  }
  
  type Writer interface {
      Write(p []byte) (n int, err os.Error);
  }

.. The io package also exports several other interfaces that specify objects that can implement several such methods. For instance, there is io.ReadWriter, an interface containing both Read and Write. We could specify io.ReadWriter by listing the two methods explicitly, but it's easier and more evocative to embed the two interfaces to form the new one, like this:

ioパッケージはこのようなメソッドを実装したオブジェクトを定義するインターフェースを他にもいくつかエクスポートします。たとえば、ReadとWriteを含んだio.ReadWriterがあります。io.ReadWriterは明示的に2つのメソッドを並べることで実装することもできますが、次のように2つのインターフェースを埋め込んで新しいものを作る方が簡単かつ刺激的です。

.. code-block:: cpp

  // ReadWriteは基本的なReadとWriteメソッドをグループ化したインターフェース。
  type ReadWriter interface {
      Reader;
      Writer;
  }

..   // ReadWrite is the interface that groups the basic Read and Write methods.

.. This says just what it looks like: A ReadWriter can do what a Reader does and what a Writer does; it is a union of the embedded interfaces (which must be disjoint sets of methods). Only interfaces can be embedded within interfaces.

これは見たとおりのことを行ないます。ReadWriterはReaderとWriterが提供するものを行なうことが可能です。つまり埋め込まれたインターフェースの和集合(これは互いに素な集合でなければなりません)です。インターフェースの中にはインターフェース以外のものは埋め込めません。

.. The same basic idea applies to structs, but with more far-reaching implications. The bufio package has two struct types, bufio.Reader and bufio.Writer, each of which of course implements the analogous interfaces from package io. And bufio also implements a buffered reader/writer, which it does by combining a reader and a writer into one struct using embedding: it lists the types within the struct but does not give them field names:

基本的な考え方はstructにもあてはまりますが、こちらはより広範囲に影響を及ぼします。bufioパッケージはbufio.Reader、bufio.Writerというふたつのstruct型を持ち、それらはもちろんioパッケージのものと類似したインターフェースの実装です。bufioはバッファリングされたreader/writerも実装しますが、これはreaderとwriterを1つのstructに埋め込むことによって行なわれます。型はstruct内に並べられますが、名前は与えられません。

.. code-block:: cpp

  // ReadWriterはReaderとWriterへのポインタを保持します。
  // それがio.ReadWriterの実装となります。
  type ReadWriter struct {
      *Reader;
      *Writer;
  }

..  // ReadWriter stores pointers to a Reader and a Writer.
    // It implements io.ReadWriter.

.. This struct could be written as:

これは次のようにも書くことも可能です。

.. code-block:: cpp

  type ReadWriter struct {
      reader *Reader;
      writer *Writer;
  }

.. but then to promote the methods of the fields and to satisfy the io interfaces, we would also need to provide forwarding methods, like this::

しかし、こうしてしまうとフィールドのメソッドを使うため、そしてioインターフェースを満たすためには次のような転送メソッドを提供する必要があるでしょう。

.. code-block:: cpp

  func (rw *ReadWriter) Read(p []byte) (n int, err os.Error) {
      return rw.reader.Read(p)
  }

.. By embedding the structs directly, we avoid this bookkeeping. The methods of embedded types come along for free, which means that bufio.ReadWriter not only has the methods of bufio.Reader and bufio.Writer, it also satisfies all three interfaces: io.Reader, io.Writer, and io.ReadWriter.

structを直接埋め込むことにより、このbookkeepingを避けられます。埋め込まれた型のメソッドはただで手に入ります。つまりbufio.ReadWriterはbufio.Readerとbufio.Writerのメソッドを持つだけではなく、io.Reader、io.Writer、io.ReadWriterのインターフェースを満たします。

.. There's an important way in which embedding differs from subclassing. When we embed a type, the methods of that type become methods of the outer type, but when they are invoked the receiver of the method is the inner type, not the outer one. In our example, when the Read method of a bufio.ReadWriter is invoked, it has exactly the same effect as the forwarding method written out above; the receiver is the reader field of the ReadWriter, not the ReadWriter itself.

埋め込みとサブクラスは重要な点で異なります。型が埋め込まれる時、その型のメソッドは外側の型のメソッドとなります。しかしそれらが呼び出される時、メソッドの受け取り側は内側の型であり、外側のものではありません。例では、bufio.ReadWriterのReadメソッドが呼び出される時、ちょうど前に書いた転送メソッドと同様の動作をします。つまり、受け側はReadWriterのreaderフィールドでありReadWriter自体ではありません。

.. Embedding can also be a simple convenience. This example shows an embedded field alongside a regular, named field:

埋め込みは簡単で便利なものです。次の例では埋め込みフィールドと通常の名前つきフィールドとが一緒になったものです:

.. code-block:: cpp

  type Job struct {
      Command    string;
      *log.Logger;
  }

.. The Job type now has the Log, Logf and other methods of log.Logger. We could have given the Logger a field name, of course, but it's not necessary to do so. And now we can log to a Job:

Job型はLogとLogf、そしてlog.Loggerのメソッドを持ちます。もちろんLoggerにフィールド名をつけることも出来ますが、なくてもかまいません。これで次のようにログを書き出せます。

.. code-block:: cpp

  job.Log("starting now...");

.. The Logger is a regular field of the struct and we can initialize it in the usual way:

Loggerはstruct内の普通のフィールドであり、いつもの方法で初期化できます。

.. code-block:: cpp

  func NewJob(command string, logger *log.Logger) *Job {
      return &Job{command, logger}
  }

.. If we need to refer to an embedded field directly, the type name of the field, ignoring the package qualifier, serves as a field name. If we needed to access the \*log.Logger of a Job variable job, we would write job.Logger. This would be useful if we wanted to refine the methods of Logger::

埋め込まれたフィールドを直接参照する必要がある場合、そのフィールドの型名のパッケージ修飾を省いた形がフィールド名となります。Job変数、jobの\*log.Loggerにアクセスする場合job.Loggerとなるでしょう。これはLoggerのメソッドを改良する際に便利です。

.. code-block:: cpp

  func (job *Job) Logf(format string, args ...) {
      job.Logger.Logf("%q: %s", job.Command, fmt.Sprintf(format, args));
  }

.. Embedding types introduces the problem of name conflicts but the rules to resolve them are simple. First, a field or method X hides any other item X in a more deeply nested part of the type. If log.Logger contained a field or method called Command, the Command field of Job would dominate it.

埋め込み型名前の衝突問題を発生させますが、それらを解決するルールは簡単なものです。まず、フィールドまたはメソッドXは奥深くにネストされた型のXを隠してしまいます。もしlog.LoggerがCommandというフィールドやメソッドを含んでいたら、JobのCommandフィールドが優先されます。

.. Second, if the same name appears at the same nesting level, it is usually an error; it would be erroneous to embed log.Logger if Job struct contained another field or method called Logger. However, if the duplicate name is never mentioned in the program outside the type definition, it is OK. This qualification provides some protection against changes made to types embedded from outside; there is no problem if a field is added that conflicts with another field in another subtype if neither field is ever used.

次に、同じ階層に同じ名前が現われる場合は通常エラーとなります。Job structがLoggerというフィールドやメソッドを持つ時にlog.Loggerを埋め込むのは間違いでしょう。しかしその重複した名前が型定義の外で振れられない場合は問題とはなりません。これは外側で埋め込まれる型からの保護を提供します。そのフィールドが使用されない限りは名前が他の下位の型と衝突するものであっても問題はありません。

.. Concurrency
   ===========

並列処理
========

.. Share by communicating
   ----------------------

通信による共有
--------------

.. Concurrent programming is a large topic and there is space only for some Go-specific highlights here.

並列プログラミングは大きなテーマなので、ここではGoに特化したハイライトだけを紹介します。

.. Concurrent programming in many environments is made difficult by the subtleties required to implement correct access to shared variables. Go encourages a different approach in which shared values are passed around on channels and, in fact, never actively shared by separate threads of execution. Only one goroutine has access to the value at any given time. Data races cannot occur, by design. To encourage this way of thinking we have reduced it to a slogan:

共有する変数に正しくアクセスする手段を提供するのに必要となる繊細さにより、多くの環境で並列プログラミングは難しいものとなっています。Goは異なるアプローチをとり、共有する値はチャンネル上でやりとりされ、実際スレッド間で活発に共有されることはありません。ある値へのアクセスはどんなときでもひとつのGoroutineしか持たず、設計上競合状態になることはありえません。このような考え方を奨励するため、次のスローガンにまとめました。\ ::

   共有メモリを使って通信せず、通信によってメモリを共有せよ。

.. Do not communicate by sharing memory; instead, share memory by communicating.

.. This approach can be taken too far. Reference counts may be best done by putting a mutex around an integer variable, for instance. But as a high-level approach, using channels to control access makes it easier to write clear, correct programs.

このアプローチは過剰だととることもできます。たとえばリファレンスカウントは整数の変数にmutexを使うことで最もうまく行なえるかもしれません。しかし、高レベルなアプローチではチャンネルを使ってアクセスを制御する方が明解で正しいプログラムをより簡単に書くことができます。

.. One way to think about this model is to consider a typical single-threaded program running on one CPU. It has no need for synchronization primitives. Now run another such instance; it too needs no synchronization. Now let those two communicate; if the communication is the synchronizer, there's still no need for other synchronization. Unix pipelines, for example, fit this model perfectly. Although Go's approach to concurrency originates in Hoare's Communicating Sequential Processes (CSP), it can also be seen as a type-safe generalization of Unix pipes.

このモデルについて考えてみるため、1つのCPU上で走る典型的なシングルスレッドのプログラムについて考察してみましょう。これに同期プリミティブは必要ありません。もうひとつそのようなプログラムを走らせてみましょう。これもまた同期は不要です。ではこれらを互いに通信させてみましょう。その通信がsynchronizerであれば、まだ同期は不要です。たとえばUnixのパイプラインがこのモデルに完全にあてはまります。Goの並列処理のアプローチはHoareのCommunicating Sequential Processes (CSP)に由来するものですが、Unixパイプのtype-safe generalizationとしてみることもできるでしょう。

Goroutines
----------

.. They're called goroutines because the existing terms—threads, coroutines, processes, and so on—convey inaccurate connotations. A goroutine has a simple model: it is a function executing in parallel with other goroutines in the same address space. It is lightweight, costing little more than the allocation of stack space. And the stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.

スレッド、コルーチン、プロセスなどの既存の用語は誤った意味合いを与えるので、goroutineと呼ばれます。goroutineは簡単なモデルで、他のgoroutineと同一のアドレススペース内で並列に実行される関数です。これは軽量でスタックスペースより少し多いほどのコストしかかかりません。スタックは小さく始まるためコストが小さく、必要に応じてヒープ領域を確保(または解放)することにより大きくなります。

.. Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management.

goroutineは複数のOSスレッド上に多重化されているので、そのうちのひとつが入出力の待ち状態でブロックされているときでも他のものは実行し続けられます。これによりはスレッドの生成、管理に関する多くの複雑性は隠されます。

.. Prefix a function or method call with the go keyword to run the call in a new goroutine. When the call completes, the goroutine exits, silently. (The effect is similar to the Unix shell's & notation for running a command in the background.)

関数やメソッドの呼び出しの前に\ :keyword:`go`\ キーワードを置くと、呼び出しを新規のgoroutine内で実行することができます。処理が完了するとgoroutineは無言で終了します。(コマンドをバックグラウンドで起動するUnixシェルの&記法に似ています。)

.. code-block:: cpp

  go list.Sort();  // list.Sortを並列に実行し、完了を待たない。

.. go list.Sort();  // run list.Sort in parallel; don't wait for it. 

.. A function literal can be handy in a goroutine invocation.

関数リテラルはgoroutineの起動をする際に重宝するでしょう。

.. code-block:: cpp

  func Announce(message string, delay int64) {
      go func() {
          time.Sleep(delay);
          fmt.Println(message);
      }()  // 括弧に注目。関数は呼び出される必要がある。
  }

..      }()  // Note the parentheses - must call the function.

.. In Go, function literals are closures: the implementation makes sure the variables referred to by the function survive as long as they are active.

Goでは関数リテラルはクロージャなので、関数内で参照される変数はそれがアクティブな間存在することが保証されます。

.. These examples aren't too practical because the functions have no way of signaling completion. For that, we need channels.

以上の例は関数側からの処理の終了を通知する方法がないため、十分に実用的なものではありません。そのためにはチャンネルが必要となります。

Channels
--------

Like maps, channels are a reference type and are allocated with make. If an optional integer parameter is provided, it sets the buffer size for the channel. The default is zero, for an unbuffered or synchronous channel::

  ci := make(chan int);            // unbuffered channel of integers
  cj := make(chan int, 0);         // unbuffered channel of integers
  cs := make(chan *os.File, 100);  // buffered channel of pointers to Files

Channels combine communication?the exchange of a value?with synchronization?guaranteeing that two calculations (goroutines) are in a known state.

There are lots of nice idioms using channels. Here's one to get us started. In the previous section we launched a sort in the background. A channel can allow the launching goroutine to wait for the sort to complete::

  c := make(chan int);  // Allocate a channel.
  // Start the sort in a goroutine; when it completes, signal on the channel.
  go func() {
      list.Sort();
      c <- 1;  // Send a signal; value does not matter. 
  }();
  doSomethingForAWhile();
  <-c;   // Wait for sort to finish; discard sent value.

Receivers always block until there is data to receive. If the channel is unbuffered, the sender blocks until the receiver has received the value. If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; if the buffer is full, this means waiting until some receiver has retrieved a value.

A buffered channel can be used like a semaphore, for instance to limit throughput. In this example, incoming requests are passed to handle, which sends a value into the channel, processes the request, and then receives a value from the channel. The capacity of the channel buffer limits the number of simultaneous calls to process::

  var sem = make(chan int, MaxOutstanding)
  
  func handle(r *Request) {
      sem <- 1;    // Wait for active queue to drain.
      process(r);  // May take a long time.
      <-sem;       // Done; enable next request to run.
  }
  
  func Serve(queue chan *Request) {
      for {
          req := <-queue;
          go handle(req);  // Don't wait for handle to finish.
      }
  }

Here's the same idea implemented by starting a fixed number of handle goroutines all reading from the request channel. The number of goroutines limits the number of simultaneous calls to process. This Serve function also accepts a channel on which it will be told to exit; after launching the goroutines it blocks receiving from that channel::

  func handle(queue chan *Request) {
      for r := range queue {
          process(r);
      }
  }
  
  func Serve(clientRequests chan *clientRequests, quit chan bool) {
      // Start handlers
      for i := 0; i < MaxOutstanding; i++ {
          go handle(clientRequests)
      }
      <-quit;    // Wait to be told to exit.
  }

Channels of channels
--------------------

One of the most important properties of Go is that a channel is a first-class value that can be allocated and passed around like any other. A common use of this property is to implement safe, parallel demultiplexing.

In the example in the previous section, handle was an idealized handler for a request but we didn't define the type it was handling. If that type includes a channel on which to reply, each client can provide its own path for the answer. Here's a schematic definition of type Request::

  type Request struct {
      args  []int;
      f    func([]int) int;
      resultChan    chan int;
  }

The client provides a function and its arguments, as well as a channel inside the request object on which to receive the answer::

  func sum(a []int) (s int) {
      for _, v := range a {
          s += v
      }
      return
  }
  
  request := &Request{[]int{3, 4, 5}, sum, make(chan int)}
  // Send request
  clientRequests <- request;
  // Wait for response.
  fmt.Printf("answer: %d\n", <-request.resultChan);

On the server side, the handler function is the only thing that changes::

  func handle(queue chan *Request) {
      for req := range queue {
          req.resultChan <- req.f(req.args);
      }
  }

There's clearly a lot more to do to make it realistic, but this code is a framework for a rate-limited, parallel, non-blocking RPC system, and there's not a mutex in sight.

Parallelization
---------------

Another application of these ideas is to parallelize a calculation across multiple CPU cores. If the calculation can be broken into separate pieces, it can be parallelized, with a channel to signal when each piece completes.

Let's say we have an expensive operation to perform on a vector of items, and that the value of the operation on each item is independent, as in this idealized example::

  type Vector []float64

  // Apply the operation to n elements of v starting at i.
  func (v Vector) DoSome(i, n int, u Vector, c chan int) {
      for ; i < n; i++ {
          v[i] += u.Op(v[i])
      }
      c <- 1;    // signal that this piece is done
  }

We launch the pieces independently in a loop, one per CPU. They can complete in any order but it doesn't matter; we just count the completion signals by draining the channel after launching all the goroutines::

  const NCPU = 4    // number of CPU cores
  
  func (v Vector) DoAll(u Vector) {
      c := make(chan int, NCPU);  // Buffering optional but sensible.
      for i := 0; i < NCPU; i++ {
          go v.DoSome(i*len(v)/NCPU, (i+1)*len(v)/NCPU, u, c);
      }
      // Drain the channel.
      for i := 0; i < NCPU; i++ {
          <-c    // wait for one task to complete
      }
      // All done.
  }

A leaky buffer
--------------

The tools of concurrent programming can even make non-concurrent ideas easier to express. Here's an example abstracted from an RPC package. The client goroutine loops receiving data from some source, perhaps a network. To avoid allocating and freeing buffers, it keeps a free list, and uses a buffered channel to represent it. If the channel is empty, a new buffer gets allocated. Once the message buffer is ready, it's sent to the server on serverChan::

  var freeList = make(chan *Buffer, 100)
  var serverChan = make(chan *Buffer)
  
  func client() {
      for {
          b, ok := <-freeList;  // grab a buffer if available
          if !ok {              // if not, allocate a new one
              b = new(Buffer)
          }
          load(b);              // read next message from the net
          serverChan <- b;      // send to server
      }
  }

The server loop receives messages from the client, processes them, and returns the buffer to the free list::

  func server() {
      for {
          b := <-serverChan;    // wait for work
          process(b);
          _ = freeList <- b;    // reuse buffer if room
      }
  }

The client's non-blocking receive from freeList obtains a buffer if one is available; otherwise the client allocates a fresh one. The server's non-blocking send on freeList puts b back on the free list unless the list is full, in which case the buffer is dropped on the floor to be reclaimed by the garbage collector. (The assignment of the send operation to the blank identifier makes it non-blocking but ignores whether the operation succeeded.) This implementation builds a leaky bucket free list in just a few lines, relying on the buffered channel and the garbage collector for bookkeeping.

.. Errors
.. ======

エラー
======

.. Library routines must often return some sort of error indication to the caller. As mentioned earlier, Go's multivalue return makes it easy to return a detailed error description alongside the normal return value. By convention, errors have type os.Error, a simple interface::

  .. type Error interface {
  ..     String() string;
  .. }

ライブラリルーチンはしばしば、呼び出し元へエラー情報の一種を返さなければなりません。先に述べたように、Goの複数の値を返す機能は、通常の戻り値の一緒に詳細なエラー内容を返すことを容易にします。エラーは以下に示す単純なインターフェースのos.Error型であることが規約によって決められています::

  type Error interface {
      String() string;
  }


.. A library writer is free to implement this interface with a richer model under the covers, making it possible not only to see the error but also to provide some context. For example, os.Open returns an os.PathError::

  .. // PathError records an error and the operation and
  .. // file path that caused it.
  .. type PathError struct {
  ..     Op string;    // "open", "unlink", etc.
  ..     Path string;  // The associated file.
  ..     Error Error;  // Returned by the system call.
  .. }

  .. func (e *PathError) String() string {
  ..     return e.Op + " " + e.Path + ": " + e.Error.String();
  .. }


ライブラリの作者は、このインターフェースを実装し、より高機能なモデルを秘匿して作ることが自由にできます。このことによって、単純なエラー内容を見せるだけではなく、より詳細なエラーコンテキストを提供することを可能にします。例として、os.Open が返す os.PathError を示します::

  // PathError は、エラー内容、エラー発生時の動作およびファイルパスを保持します。  
  type PathError struct {
      Op string;    // "open"、"unlink"など。
      Path string;  // エラー発生に関連するファイルパス。
      Error Error;  // システムコールによって返されるエラー内容。
  }

  func (e *PathError) String() string {
      return e.Op + " " + e.Path + ": " + e.Error.String();
  }

.. PathError's String generates a string like this::

  .. open /etc/passwx: no such file or directory

PathErrorが生成するエラーメッセージは次のようなものです::

  open /etc/passwx: no such file or directory

.. Such an error, which includes the problematic file name, the operation, and the operating system error it triggered, is useful even if printed far from the call that caused it; it is much more informative than the plain "no such file or directory".

問題のあるファイル名、動作、およびそれらによって引き起こされたオペレーティングシステムのエラーを含む、このようなエラーメッセージは、呼び出し元から相当離れている位置で発生したものであってもなお有用です。なぜなら、簡素な"no such file or directory"というエラーメッセージに比べて、非常に有益だからです。

.. Callers that care about the precise error details can use a type switch or a type assertion to look for specific errors and extract details. For PathErrors this might include examining the internal Error field for recoverable failures::

  .. for try := 0; try < 2; try++ {
  ..     file, err = os.Open(filename, os.O_RDONLY, 0);
  ..     if err == nil {
  ..         return
  ..     }
  ..     if e, ok := err.(*os.PathError); ok && e.Error == os.ENOSPC {
  ..         deleteTempFiles();  // Recover some space.
  ..         continue
  ..     }
  ..     return
  .. }

呼び出し元で、正確で詳細なエラー内容を必要とする場合は、type switch文や型アサーションによって、特定のエラー時の詳細なエラー内容を得ることができます。PathErrorsの場合は、復旧可能なエラーであるかどうかを、内部のエラー内容フィールドによって調べることができる場合があります::

  for try := 0; try < 2; try++ {
      file, err = os.Open(filename, os.O_RDONLY, 0);
      if err == nil {
          return
      }
      if e, ok := err.(*os.PathError); ok && e.Error == os.ENOSPC {
          deleteTempFiles();  // Recover some space.
          continue
      }
      return
  }

.. A web server
.. ============

ウェブサーバ
============

.. Let's finish with a complete Go program, a web server. This one is actually a kind of web re-server. Google provides a service at http://chart.apis.google.com that does automatic formatting of data into charts and graphs. It's hard to use interactively, though, because you need to put the data into the URL as a query. The program here provides a nicer interface to one form of data: given a short piece of text, it calls on the chart server to produce a QR code, a matrix of boxes that encode the text. That image can be grabbed with your cell phone's camera and interpreted as, for instance, a URL, saving you typing the URL into the phone's tiny keyboard.

最後に、Goの完全なプログラムとして、ウェブサーバを作ってみましょう。このプログラムは、実際にはウェブ中継サーバの一種です。Googleは、データを自動的に書式・体裁を整え、図表やグラフを作成するサービスを http://chart.apis.google.com にて提供しています。しかし、それは対話的に使用することは難しいです。なぜなら、クエリとしてURLにデータを埋め込む必要があるためです。これから作成するプログラムは、データの一形式を作成する優れたインターフェースを提供します。短いテキストが与えられると、プログラムはQRコード（与えられたテキストがエンコードされた粒子状の四角形）を生成するようにチャートサーバにリクエストをします。生成されたQRコードは携帯電話のカメラによって取り込むことができます。取り込まれたQRコードは、例えば（携帯電話のとても小さいキーでURLを打つのを省略するための）URLとして解読されます。

.. Here's the complete program. An explanation follows::

  .. package main
  
  .. import (
  ..     "flag";
  ..     "http";
  ..     "io";
  ..     "log";
  ..     "strings";
  ..     "template";
  .. )
  
  .. var addr = flag.String("addr", ":1718", "http service address") // Q=17, R=18
  .. var fmap = template.FormatterMap{
  ..     "html": template.HtmlFormatter,
  ..     "url+html": UrlHtmlFormatter,
  .. }
  .. var templ = template.MustParse(templateStr, fmap)
  
  .. func main() {
  ..     flag.Parse();
  ..     http.Handle("/", http.HandlerFunc(QR));
  ..     err := http.ListenAndServe(*addr, nil);
  ..     if err != nil {
  ..         log.Exit("ListenAndServe:", err);
  ..     }
  .. }
  
  .. func QR(c *http.Conn, req *http.Request) {
  ..     templ.Execute(req.FormValue("s"), c);
  .. }
  
  .. func UrlHtmlFormatter(w io.Writer, v interface{}, fmt string) {
  ..     template.HtmlEscape(w, strings.Bytes(http.URLEscape(v.(string))));
  .. }
  
  
  .. const templateStr = `
  .. <html>
  .. <head>
  .. <title>QR Link Generator</title>
  .. </head>
  .. <body>
  .. {.section @}
  .. <img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={@|url+html}"
  .. />
  .. <br>
  .. {@|html}
  .. <br>
  .. <br>
  .. {.end}
  .. <form action="/" name=f method="GET"><input maxLength=1024 size=70
  .. name=s value="" title="Text to QR Encode"><input type=submit
  .. value="Show QR" name=qr>
  .. </form>
  .. </body>
  .. </html>
  .. `

以下に示すのが、プログラム全体です。 ソースコードの後で、プログラムについて説明します::

  package main
  
  import (
      "flag";
      "http";
      "io";
      "log";
      "strings";
      "template";
  )
  
  var addr = flag.String("addr", ":1718", "http service address") // Q=17, R=18
  var fmap = template.FormatterMap{
      "html": template.HTMLFormatter,
      "url+html": UrlHtmlFormatter,
  }
  var templ = template.MustParse(templateStr, fmap)
  
  func main() {
      flag.Parse();
      http.Handle("/", http.HandlerFunc(QR));
      err := http.ListenAndServe(*addr, nil);
      if err != nil {
          log.Exit("ListenAndServe:", err);
      }
  }
  
  func QR(c *http.Conn, req *http.Request) {
      templ.Execute(req.FormValue("s"), c);
  }
  
  func UrlHtmlFormatter(w io.Writer, v interface{}, fmt string) {
      template.HTMLEscape(w, strings.Bytes(http.URLEscape(v.(string))));
  }
  
  
  const templateStr = `
  <html>
  <head>
  <title>QR Link Generator</title>
  </head>
  <body>
  {.section @}
  <img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={@|url+html}"
  />
  <br>
  {@|html}
  <br>
  <br>
  {.end}
  <form action="/" name=f method="GET"><input maxLength=1024 size=70
  name=s value="" title="Text to QR Encode"><input type=submit
  value="Show QR" name=qr>
  </form>
  </body>
  </html>
  `

.. The pieces up to main should be easy to follow. The one flag sets a default HTTP port for our server. The template variable templ is where the fun happens. It builds an HTML template that will be executed by the server to display the page; more about that in a moment.

上記のソースコードは、main関数まで、処理内容を容易に追うことができるはずです。flagはこのサーバのデフォルトHTTPポートを設定します。テンプレート変数のtemplでは、面白いことが起こります。このテンプレート変数は、ページを表示するためにサーバにより実行されるHTMLテンプレートを生成します。詳細は、すぐ後で説明します。

.. The main function parses the flags and, using the mechanism we talked about above, binds the function QR to the root path for the server. Then http.ListenAndServe is called to start the server; it blocks while the server runs.

main関数はフラグ変数を処理してから、上述したメカニズムを用いて、このサーバのルート(/)パスにQR関数をバインドします。そして、このサーバを起動するためにhttp.ListenAndServe関数を呼びます。この関数は、サーバが起動している間は排他的に動作します。

.. QR just receives the request, which contains form data, and executes the template on the data in the form value named s.

QR関数がフォームデータを含むリクエストを受け取ると、フォームデータのsパラメータの値に基づいて、テンプレートを処理して出力します。

.. The template package, inspired by json-template, is powerful; this program just touches on its capabilities. In essence, it rewrites a piece of text on the fly by substituting elements derived from data items passed to templ.Execute, in this case the form value. Within the template text (templateStr), brace-delimited pieces denote template actions. The piece from the {.section @} to {.end} executes with the value of the data item @, which is a shorthand for “the current item”, which is the form value. (When the string is empty, this piece of the template is suppressed.)

json-templateに影響を受けたtemplateパッケージは強力です。このプログラムは、単にtemplateパッケージの能力を、ほんの一部使用しているだけです。端的に説明すると、templ.Executeに渡されたデータ（この場合はフォームデータ）から得た要素に逐次代入することで、テキストを書き換えています。テンプレートテキスト(templateStr)においては、波括弧{}で囲まれた要素がテンプレートアクションであることを意味します。 ``{.section @}`` から ``{.end}`` の間の部分はデータ@とともに処理されます。@は『現在の要素』の簡便な記法です。現在の要素、とはフォームの値のことです。（もしフォームの値が空の場合には、この部分（ ``{.section @}`` から ``{.end}`` の間）は出力されません。）

.. The snippet {@|url+html} says to run the data through the formatter installed in the formatter map (fmap) under the name "url+html". That is the function UrlHtmlFormatter, which sanitizes the string for safe display on the web page.

``{@|url+html}`` という記述は、フォーマッタマップ(fmap)に"url+html"という名前でインストールされたフォーマッタを利用してデータを処理するように命令します。

.. The rest of the template string is just the HTML to show when the page loads. If this is too quick an explanation, see the documentation for the template package for a more thorough discussion.

テンプレートの残りのテキストは、ページが読み込まれる際に表示される単なるHTMLです。もし、これまでの説明が不十分だと感じるなら、templateパッケージのより徹底的な解説ドキュメントを参照してください。

.. And there you have it: a useful webserver in a few lines of code plus some data-driven HTML text. Go is powerful enough to make a lot happen in a few lines.

そして、あなたはわずかな行数のコードに加えて、いくらかのデータ駆動のHTMLテキストを記述することで、便利なウェブサーバを手に入れることができました。Goはわずかな行数で、非常に多彩な機能を実現できる強力な言語なのです。

.. Except as noted, this content is licensed under Creative Commons Attribution 3.0.

特に断りが無い限り、このドキュメント内容は、Creative Commons Attribution 3.0の元にライセンスされます。
