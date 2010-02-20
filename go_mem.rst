.. The Go Memory Model

====================
Go言語のメモリモデル
====================

.. Introduction

イントロダクション
==================

.. The Go memory model specifies the conditions under which reads of a variable in one goroutine can be guaranteed to observe values produced by writes to the same variable in a different goroutine.

Go言語のメモリモデルは、goroutineでの変数の更新結果が別のgoroutineでの同一変数の参照に認識される保証の条件を示します。

.. Happens Before

Happens-Before
==============

.. Within a single goroutine, reads and writes must behave as if they executed in the order specified by the program. That is, compilers and processors may reorder the reads and writes executed within a single goroutine only when the reordering does not change the behavior within that goroutine as defined by the language specification. Because of this reordering, the execution order observed by one goroutine may differ from the order perceived by another. For example, if one goroutine executes ``a = 1; b = 2;``, another might observe the updated value of ``b`` before the updated value of ``a``. 

単一のgoroutine内での参照および更新は、プログラムによって指定された順序通りに実行されるよう振る舞うでしょう。参照および更新の並べ替えが言語仕様で定義された挙動を変えない限りにおいては、コンパイラとプロセッサは単一のgoroutine内での並べ替えをおこなっても構いません。この並べ替えによって、goroutineが認識している実行順序が別のgoroutineが認識する順序と異なっていてもいいのです。例えば、goroutineが ``a = 1; b = 2;`` を実行する場合、別のgoroutineが ``a`` の前に ``b`` の更新結果を認識するかもしれません。

.. To specify the requirements of reads and writes, we define *happens before*, a partial order on the execution of memory operations in a Go program. If event *e* 1 happens before event *e* 2, then we say that *e* 2 happens after *e* 1. Also, if *e* 1 does not happen before *e* 2 and does not happen after *e* 2, then we say that *e* 1 and *e* 2 happen concurrently. 

参照および更新の要求仕様を示すため、Go言語プログラムでのメモリ操作実行に関する部分的な順序関係、 *happens-before* を定義します。イベント *e* 1がイベント *e* 2より前に発生する場合、 *e* 2は *e* 1の後に発生すると言えます。また、 *e* 1が *e* 2の前に発生せず、 *e* 2の後に発生しない場合、 *e* 1と *e* 2は同時に発生すると言えます。

.. Within a single goroutine, the happens before order is the order expressed by the program.

単一のgoroutine内では、このhappens-beforeの順序関係はプログラムで記述された通りとなります。

.. A read *r* of a variable ``v`` is *allowed* to observe a write *w* to ``v`` if both of the following hold: 

変数 ``v`` の更新 *w* を参照 *r* が *認識できる* のは、以下の２つが成り立つ場合です。

.. 1. *w* happens before *r*.
.. 2. There is no other write *w'* to ``v`` that happens after *w* but before *r*.

1. *w* は *r* の前に発生する
2. *w* の後、 *r* の前に発生するような、 ``v`` に対する別の更新 *w'* が無い

.. To guarantee that a read *r* of a variable ``v`` observes a particular write *w* to ``v``, ensure that *w* is the only write *r* is allowed to observe. That is, *r* is *guaranteed* to observe *w* if both of the following hold:

変数 ``v`` の参照 *r* が特定の更新 *w* を認識することを保証するためには、 *r* が認識できる唯一の更新が *w* であることを確実にする必要があります。つまり、 *r* が *w* を認識することが保証されるのは、以下の２つが成り立つ場合です。

.. 1. *w* happens before *r*.
.. 2. Any other write to the shared variable ``v`` either happens before *w* or after *r*.

1. *w* は *r* の前に発生する
2. 共有変数 ``v`` への他の更新すべてが、 *w* の前に発生するか *r* の後に発生する

.. This pair of conditions is stronger than the first pair; it requires that there are no other writes happening concurrently with *w* or *r*.

このペアの条件は最初のペアより優先されます。 *w* あるいは *r* と同時に発生する他の更新が無いことが必要です。

.. Within a single goroutine, there is no concurrency, so the two definitions are equivalent: a read *r* observes the value written by the most recent write *w* to ``v``. When multiple goroutines access a shared variable ``v``, they must use synchronization events to establish happens-before conditions that ensure reads observe the desired writes.

単一のgoroutine内では並列処理は無いため、この２つの定義は同じ意味になります。参照 *r* は ``v`` に対する最新の更新 *w* の値を認識します。複数のgoroutineが共有変数 ``v`` にアクセスする場合、必ず同期イベントを使用して、望ましい更新結果を参照が確実に認識するよう「happens-before」の条件を成立させます。

.. The initialization of variable ``v`` with the zero value for ``v``'s type behaves as a write in the memory model.

変数 ``v`` をその型に合わせたゼロ値に初期化する処理は、メモリモデルの中では更新として振舞います。

.. Reads and writes of values larger than a single machine word behave as multiple machine-word-sized operations in an unspecified order. 

そのマシンの１ワードより大きい値の参照および更新は、順不同の複数のマシンワードサイズの処理として振舞います。

.. Synchronization

同期
====

.. Initialization

初期化処理
----------

.. Program initialization runs in a single goroutine and new goroutines created during initialization do not start running until initialization ends.

プログラムの初期化処理は単一のgoroutineで実行され、初期化処理が完了するまでは初期化処理中に生成された新しいgoroutineの実行は開始されません。

.. *If a package* ``p`` *imports package* ``q`` *, the completion of* ``q`` *'s* ``init`` *functions happens before the start of any of* ``p`` *'s.*

*パッケージ* ``p`` *がパッケージ* ``q`` *をインポートする場合、* ``q`` *の* ``init`` *関数の完了は* ``p`` *の* ``init`` *関数すべての開始の前に発生します。*

.. *The start of the function* ``main.main`` *happens after all* ``init`` *functions have finished.*

*関数* ``main.main`` *の開始は、すべての* ``init`` *関数の完了後に発生します。*

.. *The execution of any goroutines created during* ``init`` *functions happens after all* ``init`` *functions have finished.*

``init`` *関数実行中に生成されたすべてのgoroutineの実行は、すべての* ``init`` *関数の完了後に発生します。*


.. Goroutine creation

goroutineの生成
------------------

.. *The* ``go`` *statement that starts a new goroutine happens before the goroutine's execution begins.*

*新たなgoroutineを開始する* ``go`` *ステートメントは、goroutineの実行の開始前に発生します。*

.. For example, in this program:

.. code-block:: cpp

    var a string;

    func f() {
    	print(a);
    }

    func hello() {
    	a = "hello, world";
    	go f();
    }

.. calling ``hello`` will print ``"hello, world"`` at some point in the future (perhaps after ``hello`` has returned). 

例えばこのプログラムでは、 ``hello`` 関数の呼び出しは、未来のある時点(おそらく ``hello`` 関数から戻った後)で ``"hello, world"`` を印字します。

.. Channel communication

チャンネル通信
--------------

.. Channel communication is the main method of synchronization between goroutines. Each send on a particular channel is matched to a corresponding receive from that channel, usually in a different goroutine.

チャンネル通信はgoroutine間の同期のメインの手段です。特定チャンネル上のそれぞれの送信は、(通常は異なるgoroutineでの)該当チャンネルからの対応する受信とマッチします。

.. *A send on a channel happens before the corresponding receive from that channel completes.*

*チャンネル上の送信は、該当チャンネルからの対応する受信が完了する前に発生します。*

.. This program:

.. code-block:: cpp

    var c = make(chan int, 10)
    var a string

    func f() {
    	a = "hello, world";
    	c <- 0;
    }

    func main() {
    	go f();
    	<-c;
    	print(a);
    }

.. is guaranteed to print ``"hello, world"``. The write to ``a`` happens before the send on ``c``, which happens before the corresponding receive on ``c`` completes, which happens before the ``print``.

このプログラムは ``"hello, world"`` の印字を保証します。 ``a`` の更新は ``c`` への送信の前に発生します。 ``c`` への送信は対応する受信完了前に発生し、受信は ``print`` 実行前に発生します。

.. *A receive from an unbuffered channel happens before the send on that channel completes.*

*バッファリング無しのチャンネルからの受信は、該当チャンネル上の送信完了前に発生します。*

.. This program:

.. code-block:: cpp

    var c = make(chan int)
    var a string

    func f() {
    	a = "hello, world";
    	<-c;
    }

    func main() {
    	go f();
    	c <- 0;
    	print(a);
    }

.. is also guaranteed to print ``"hello, world"``. The write to ``a`` happens before the receive on ``c``, which happens before the corresponding send on ``c`` completes, which happens before the ``print``.

このプログラムも ``"hello, world"`` の印字を保証します。 ``a`` の更新は ``c`` からの受信の前に発生します。 ``c`` からの受信は対応する送信完了の前に発生し、送信は ``print`` 実行前に発生します。

.. If the channel were buffered (e.g., ``c = make(chan int, 1)``) then the program would not be guaranteed to print ``"hello, world"``. (It might print the empty string; it cannot print ``"hello, sailor"``, nor can it crash.) 

チャンネルがバッファリングされていた場合(例: ``c = make(chan int, 1)``)、プログラムは ``"hello, world"`` の印字を保証しないでしょう(``"hello, sailor"`` と印字したりクラッシュすることはありませんが、空文字列を印字するでしょう)。

.. Locks

ロック
------

.. The ``sync`` package implements two lock data types, ``sync.Mutex`` and ``sync.RWMutex``.

``sync`` パッケージは ``sync.Mutex`` および ``sync.RWMutex`` の２つのロックデータ型を実装しています。

.. *For any* ``sync.Mutex`` *or* ``sync.RWMutex`` *variable* ``l`` *and n < m, the n'th call to* ``l.Unlock()`` *happens before the m'th call to* ``l.Lock()`` *returns.*

``sync.Mutex`` *あるいは* ``sync.RWMutex`` *のすべての変数* ``l`` *に対して、n < mが成り立つ場合に、* ``l.Unlock()`` *のn回目の呼び出しは、*  ``l.Unlock()`` *のm回目の呼び出しのリターンの前に発生します。*

.. This program:

.. code-block:: cpp

    var l sync.Mutex
    var a string

    func f() {
    	a = "hello, world";
    	l.Unlock();
    }

    func main() {
    	l.Lock();
    	go f();
    	l.Lock();
    	print(a);
    }

.. is guaranteed to print ``"hello, world"``. The first call to ``l.Unlock()`` (in ``f``) happens before the second call to ``l.Lock()`` (in ``main``) returns, which happens before the ``print``.

このプログラムは ``"hello, world"`` の印字を保証します。関数 ``f`` の ``l.Unlock()`` の最初の呼び出しは、関数 ``main`` の ``l.Lock()`` の２回目の呼び出しのリターン前に発生します。その後、 ``print`` が実行されます。

.. *For any call to* ``l.RLock`` *on a* ``sync.RWMutex`` *variable* ``l``, *there is an n such that the* ``l.RLock`` *happens (returns) after the n'th call to* ``l.Unlock`` *and the matching* ``l.RUnlock`` *happens before the n+1'th call to* ``l.Lock``.

``sync.RWMutex`` *の変数* ``l`` *に対するすべての* ``l.RLock`` *の呼び出しについて、* ``l.RLock`` *がn回目の* ``l.Unlock`` *の呼び出しの後に発生(つまりリターン)し、それと対応する* ``l.RUnlock`` *はn+1回目の* ``l.Lock`` *の呼び出しの前に発生します。*

Once
----

.. The ``once`` package provides a safe mechanism for initialization in the presence of multiple goroutines. Multiple threads can execute ``once.Do(f)`` for a particular ``f``, but only one will run ``f()``, and the other calls block until ``f()`` has returned.

``once`` パッケージは複数goroutine環境下での初期化処理の安全なメカニズムを提供します。複数のスレッドは特定の関数 ``f`` に対する ``once.Do(f)`` を実行できますが、実際に ``f()`` を実行するのは唯一の呼び出しだけであり、他は ``f()`` のリターンまでブロックされます。

.. *A single call of* ``f()`` *from* ``once.Do(f)`` *happens (returns) before any call of* ``once.Do(f)`` *returns.*

``once.Do(f)`` *からの* ``f()`` *の１回だけの呼び出しは、すべての* ``once.Do(f)`` *呼び出しのリターンの前に発生(リターン)します。*

.. In this program:

.. code-block:: cpp

    var a string

    func setup() {
    	a = "hello, world";
    }

    func doprint() {
    	once.Do(setup);
    	print(a);
    }

    func twoprint() {
    	go doprint();
    	go doprint();
    }

.. calling ``twoprint`` causes ``"hello, world"`` to be printed twice. The first call to ``twoprint`` runs ``setup`` once. 

このプログラムでは、 ``twoprint`` 呼び出しにより ``"hello, world"`` が２回印字されます。
``twoprint`` の最初の呼び出しが、 ``setup`` を１回だけ実行します。


.. Incorrect synchronization

不正な同期
==========

.. Note that a read *r* may observe the value written by a write *w* that happens concurrently with *r*. Even if this occurs, it does not impl that reads happening after *r* will observe writes that happened before *w*.

参照 *r* と同時に発生した更新 *w* によって書き込まれた値を、参照 *r* が認識するかもしれないことに注意してください。これが起こったとしても、必ずしも *r* の後に発生する参照が *w* の前に発生した更新を認識することを意味するわけではありません。

.. In this program:

.. code-block:: cpp

    var a, b int

    func f() {
    	a = 1;
    	b = 2;
    }

    func g() {
    	print(b);
    	print(a);
    }

    func main() {
    	go f();
    	g();
    }

.. it can happen that ``g`` prints ``2`` and then ``0``.

このプログラムでは、関数 ``g`` が ``2`` そして ``0`` を印字する事態が起こり得ます。

.. This fact invalidates a few common idioms.

この事実は共通のイディオムのいくつかを無効にします。

.. Double-checked locking is an attempt to avoid the overhead of synchronization. For example, the ``twoprint`` program might be incorrectly written as:

２重チェックのロックは、同期のオーバヘッドを回避する試みです。例えば ``twoprint`` プログラムは以下のように間違って記述されるかもしれません。

.. code-block:: cpp

    var a string
    var done bool

    func setup() {
    	a = "hello, world";
    	done = true;
    }

    func doprint() {
    	if !done {
    		once.Do(setup);
    	}
    	print(a);
    }

    func twoprint() {
    	go doprint();
    	go doprint();
    }

.. but there is no guarantee that, in ``doprint``, observing the write to ``done`` implies observing the write to ``a``. This version can (incorrectly) print an empty string instead of ``"hello, world"``.

関数 ``doprint`` において、 ``done`` 更新の認識が ``a`` 更新の認識を意味する保証はありません。このバージョンでは ``"hello, world"`` の代わりに(不正に)空文字列を印字する可能性があります。

.. Another incorrect idiom is busy waiting for a value, as in:

もう1つの不正なイディオムは、値に対するビジーウェイトです。

.. code-block:: cpp

    var a string
    var done bool

    func setup() {
    	a = "hello, world";
    	done = true;
    }

    func main() {
    	go setup();
    	for !done {
    	}
    	print(a);
    }

.. As before, there is no guarantee that, in ``main``, observing the write to ``done`` implies observing the write to ``a``, so this program could print an empty string too. Worse, there is no guarantee that the write to ``done`` will ever be observed by ``main``, since there are no synchronization events between the two threads. The loop in ``main`` is not guaranteed to finish.

前述の通り、関数 ``main`` において、 ``done`` 更新の認識が ``a`` 更新の認識を意味する保証は無いため、このプログラムも空文字を印字する可能性があります。さらに、２つのスレッド間で同期イベントが無いため、関数 ``main`` が ``done`` 更新を認識する保証はありません。関数 ``main`` のループ完了は保証されません。

.. There are subtler variants on this theme, such as this program.

このテーマに関しては、他にも以下のプログラムのように油断出来ないことがいくつかあります。

.. code-block:: cpp

    type T struct {
    	msg string;
    }

    var g *T

    func setup() {
    	t := new(T);
    	t.msg = "hello, world";
    	g = t;
    }

    func main() {
    	go setup();
    	for g == nil {
    	}
    	print(g.msg);
    }

.. Even if ``main`` observes ``g != nil`` and exits its loop, there is no guarantee that it will observe the initialized value for ``g.msg``.

関数 ``main`` が ``g != nil`` を認識してループを抜けたとしても、 ``g.msg`` の初期化された値が認識される保証はありません。

.. In all these examples, the solution is the same: use explicit synchronization.

これらすべての例において、解決策は等しく「明示的な同期を使用すること」です。

