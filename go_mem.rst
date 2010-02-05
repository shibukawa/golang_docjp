===================
The Go Memory Model
===================

Introduction
============

The Go memory model specifies the conditions under which reads of a variable in one goroutine can be guaranteed to observe values produced by writes to the same variable in a different goroutine. 

Happens Before
==============
Within a single goroutine, reads and writes must behave as if they executed in the order specified by the program. That is, compilers and processors may reorder the reads and writes executed within a single goroutine only when the reordering does not change the behavior within that goroutine as defined by the language specification. Because of this reordering, the execution order observed by one goroutine may differ from the order perceived by another. For example, if one goroutine executes ``a = 1; b = 2;``, another might observe the updated value of ``b`` before the updated value of ``a``. 

To specify the requirements of reads and writes, we define *happens before*, a partial order on the execution of memory operations in a Go program. If event *e* 1 happens before event *e* 2, then we say that *e* 2 happens after *e* 1. Also, if *e* 1 does not happen before *e* 2 and does not happen after *e* 2, then we say that *e* 1 and *e* 2 happen concurrently. 

Within a single goroutine, the happens before order is the order expressed by the program.

A read *r* of a variable ``v`` is *allowed* to observe a write *w* to ``v`` if both of the following hold: 

1. *w* happens before *r*.
2. There is no other write *w'* to ``v`` that happens after *w* but before *r*.

To guarantee that a read *r* of a variable ``v`` observes a particular write *w* to ``v``, ensure that *w* is the only write *r* is allowed to observe. That is, *r* is *guaranteed* to observe *w* if both of the following hold:

1. *w* happens before *r*.
2. Any other write to the shared variable ``v`` either happens before *w* or after *r*.

This pair of conditions is stronger than the first pair; it requires that there are no other writes happening concurrently with *w* or *r*.

Within a single goroutine, there is no concurrency, so the two definitions are equivalent: a read *r* observes the value written by the most recent write *w* to ``v``. When multiple goroutines access a shared variable ``v``, they must use synchronization events to establish happens-before conditions that ensure reads observe the desired writes.

The initialization of variable ``v`` with the zero value for ``v``'s type behaves as a write in the memory model.

Reads and writes of values larger than a single machine word behave as multiple machine-word-sized operations in an unspecified order. 

Synchronization
===============

Initialization
--------------

Program initialization runs in a single goroutine and new goroutines created during initialization do not start running until initialization ends.

*If a package* ``p`` *imports package* ``q`` *, the completion of* ``q`` *'s* ``init`` *functions happens before the start of any of* ``p`` *'s.*

*The start of the function* ``main.main`` *happens after all* ``init`` *functions have finished.*

*The execution of any goroutines created during* ``init`` *functions happens after all* ``init`` *functions have finished.*

Goroutine creation
------------------

*The* ``go`` *statement that starts a new goroutine happens before the goroutine's execution begins.*

For example, in this program:

.. code-block:: cpp

    var a string;

    func f() {
    	print(a);
    }

    func hello() {
    	a = "hello, world";
    	go f();
    }

calling ``hello`` will print ``"hello, world"`` at some point in the future (perhaps after ``hello`` has returned). 

Channel communication
---------------------

Channel communication is the main method of synchronization between goroutines. Each send on a particular channel is matched to a corresponding receive from that channel, usually in a different goroutine.

*A send on a channel happens before the corresponding receive from that channel completes.*

This program:

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

is guaranteed to print ``"hello, world"``. The write to ``a`` happens before the send on ``c``, which happens before the corresponding receive on ``c`` completes, which happens before the ``print``.

*A receive from an unbuffered channel happens before the send on that channel completes.*

This program:

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

is also guaranteed to print ``"hello, world"``. The write to ``a`` happens before the receive on ``c``, which happens before the corresponding send on ``c`` completes, which happens before the ``print``.

If the channel were buffered (e.g., ``c = make(chan int, 1)``) then the program would not be guaranteed to print ``"hello, world"``. (It might print the empty string; it cannot print ``"hello, sailor"``, nor can it crash.) 

Locks
-----

The ``sync`` package implements two lock data types, ``sync.Mutex`` and ``sync.RWMutex``.

*For any* ``sync.Mutex`` *or* ``sync.RWMutex`` *variable* ``l`` *and n < m, the n'th call to* ``l.Unlock()`` *happens before the m'th call to* ``l.Lock()`` *returns.*

This program:

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

is guaranteed to print ``"hello, world"``. The first call to ``l.Unlock()`` (in ``f``) happens before the second call to ``l.Lock()`` (in ``main``) returns, which happens before the ``print``.

*For any call to* ``l.RLock`` *on a* ``sync.RWMutex`` *variable* ``l``, *there is an n such that the* ``l.RLock`` *happens (returns) after the n'th call to* ``l.Unlock`` *and the matching* ``l.RUnlock`` *happens before the n+1'th call to* ``l.Lock``.

Once
----

The ``once`` package provides a safe mechanism for initialization in the presence of multiple goroutines. Multiple threads can execute ``once.Do(f)`` for a particular ``f``, but only one will run ``f()``, and the other calls block until ``f()`` has returned.

*A single call of* ``f()`` *from* ``once.Do(f)`` *happens (returns) before any call of* ``once.Do(f)`` *returns.*

In this program:

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

calling ``twoprint`` causes ``"hello, world"`` to be printed twice. The first call to ``twoprint`` runs ``setup`` once. 

Incorrect synchronization
=========================

Note that a read *r* may observe the value written by a write *w* that happens concurrently with *r*. Even if this occurs, it does not imply that reads happening after *r* will observe writes that happened before *w*.

In this program:

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

it can happen that ``g`` prints ``2`` and then ``0``.

This fact invalidates a few common idioms.

Double-checked locking is an attempt to avoid the overhead of synchronization. For example, the ``twoprint`` program might be incorrectly written as:

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

but there is no guarantee that, in ``doprint``, observing the write to ``done`` implies observing the write to ``a``. This version can (incorrectly) print an empty string instead of ``"hello, world"``.

Another incorrect idiom is busy waiting for a value, as in:

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

As before, there is no guarantee that, in ``main``, observing the write to ``done`` implies observing the write to ``a``, so this program could print an empty string too. Worse, there is no guarantee that the write to ``done`` will ever be observed by ``main``, since there are no synchronization events between the two threads. The loop in ``main`` is not guaranteed to finish.

There are subtler variants on this theme, such as this program.

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

Even if ``main`` observes ``g != nil`` and exits its loop, there is no guarantee that it will observe the initialized value for ``g.msg``.

In all these examples, the solution is the same: use explicit synchronization. 

