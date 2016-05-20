---
title: Introducing fiberbundle: multicore concurrency for Tcl
---

Two of Tcl's greatest strengths are its integrated single-threaded event loop and its support for full coroutines. While the event loop is widely appreciated, coroutines perhaps are not. [fiberbundle](https://github.com/zpconn/fiberbundle) is a small, compact library that makes heavy use of both of these in order bring fiber-based Erlang-style multicore concurrency and parallelism to Tcl.

There are several core concepts in ```fiberbundle```:

1. A ```fiber``` is a lightweight userspace green thread of execution. Fibers do not share state and communicate with each other via message passing. They are scheduled cooperatively and must explicitly yield to the event dispatcher. Fibers are implemented as coroutines behind the scenes.
2. A ```bundle``` is a collection of fibers along with an associated cooperative scheduler.
3. A ```bundle space``` is a controlling entity that creates Tcl threads, spawns bundles inside threads (typically in a one-to-one manner), and distributes new fibers across bundles and threads. The bundle space also acts as an intermediary for orchesting the communication between fibers located in different threads.

The library provides a number of major features:

1. Creation of up to hundreds of thousands of fibers as coroutines along with a cooperative scheduling algorithm.
2. Creation and management of multiple Tcl threads behind the scenes. Fibers are automatically distributed across threads. Fibers in different threads aren't aware of this and can easily communicate as if they were in the same thread.
3. Synchronous and asynchronous agents (an agent is a fiber which stores state which can be concurrently accessed and modified by other fibers).
4. Basic worker pool functionality.
5. Fast immutable closures that can be passed to fibers and other threads.
6. Full support for nested receive loops. This is achieved while keeping the core scheduler from ever being reentrant.
7. Message type and sender whitelists for solving complex concurrency issues with nested receive loops.

As a very basic use case, the library provides easy-to-use parallelism and worker pools. Here's an example of a rather silly and intentionally inefficient script that shows how easily the library can consume all available CPU resources (it achieves >3000% CPU usage on my machine):

``` tcl
package require fiberbundle
package require fiberbundle-prelude

# Any procs defined here will be available in any fiber and in any thread.
set shared_code_buffer {
	proc fib {n} {
		if {$n <= 1} {
			return 1
		}

		return [expr {[fib [expr {$n-1}]] + [fib [expr {$n-2}]]}]
	}
}

# The universe oversees all the different threads behind the scenes.
set ::universe [::fiberbundle::universe new $shared_code_buffer]

# Inflation causes the universe to create one thread and one bundle per
# available CPU core.
$::universe inflate

# To create a fiber and run it, we just need to supply a name and a lambda
# expression for it to execute.
$::universe spawn_fiber main {{} {
	# Create an asynchronous logger which writes to a file.
	::fiberbundle::prelude::spawn_logger test.log

	set inputs [list]
	set range 100
	for {set i 0} {$i < $range} {incr i} {
		lappend inputs $i
	}

	# Waste as many CPU cores as possible computing Fibonacci numbers.
	# Note that the standard map function used here performs the computations in parallel
	# in multiple fibers, but it blocks the current fiber until all the calculations
	# complete and return results.

	set lambda {{x} { return [fib $x] }}
	send logger info "Starting map!"
	set outputs [::fiberbundle::prelude::map $inputs $lambda]
	send logger info "Output: $outputs"

	wait_forever
}}
```

Of course, this really barely scratches the surface of what's possible, and this sort of parallel computation was already possible with thread pools in Tcl. ```fiberbundle``` really shines for applications that require complex concurrent interactions between many actors and which can experience performance boosts from parallelism across multiple threads. There are more interesting and non-trivial examples on the GitHub page.

