---
title: The K combinator and effectful computations in Tcl
---

The K combinator, introduced by Donal Fellows, can be defined by the following simple procedure in Tcl:

``` tcl
proc K {a b} { set a }
```

This pops up fairly commonly on the Tcl wiki, and at first glance it's not clear what the point is. It just returns the value of its first argument and ignores the second argument. Of what use could this be? This post contains some ruminations on how to answer this question. ```K``` is mostly about how Tcl performs command substitution and can be used for optimizations in certain situations by expressing side effects without creating temporary variables.

Before illustrating the use of this with examples, I'm going to use a slightly more general form:

``` tcl
proc K {a args} { set a }
```

Remember that ```args``` in Tcl has a special meaning: it denotes a list containing all remaining arguments. So this is the K combinator extended to arbitrarily many arguments. I'm going to still call this ```K``` going forward. This extended K combinator just takes any number of arguments (at least one) and returns the value of the first argument.

Here's a warm-up to get you used to thinking about ```K``` and Tcl substitution. For instance, it can be used for a post-increment operator, which Tcl lacks:

``` tcl
K $a [incr a]
```

returns the value of ```a``` and then increments the variable. This example should suggest that the second argument of ```K``` is often used for managing side-effects. Indeed, here we are performing one action (retrieving the value of the variable ```a```) and we are introducing a side-effect of the computation (the variable ```a``` gets incremented at the end). You might even realize that the extended form of ```K``` which we are using allows us to attach an arbitrary number of side effects to any computation.

For instance, the default ```unset``` command in Tcl just unsets a variable, but perhaps we also want to get the last value the variable had before being unset. We can define a new command to do this using ```K```:

``` tcl
proc unset! {varName} {
	upvar $varName x
	K [set x] [unset x]
}
```

Here we are defining ```unset!``` as ```set``` with ```unset``` as a side effect.

Or we can define a procedure to clear the value of a variable and return to us the last value the variable had:

``` tcl
proc clear {varName} { upvar $varName x; K [set x] [set x {}] }
```

Or we can swap two variables, say ```a``` and ```b```, without any temporary variables:

``` tcl
set a [K $b [set b $a]]
```

These examples aren't particularly useful, but they illustrate how ```K``` allows us to tag side effects onto ordinary command invocations in a condensed syntax.

Here's another example. Suppose we have a stack, represented as a list in Tcl, and we want to implement the pop operation. Pop inspects the top-most value on the stack, returning its value to us, and it then deletes this element from the stack. With ```K```, this sounds like a computation with a side effect:

``` tcl
proc pop {stackName} {
	upvar $stackName stack
	K [lindex $stack end] [set stack [lrange 0 end-1]]
}
```

What's interesting here is we accomplished this operation with one less variable and one less command invocation compared to the standard approach:

``` tcl
proc pop {stackName} {
	upvar $stackName stack
	set top [lindex $stack end]
	set stack [lrange 0 end-1]
	return $top
}
```

We can also achieve an efficient implementation of lambda expressions in Tcl using ```K```:

``` tcl
proc lambda {arg body} { K [set n [info $body 0]] [proc $n $arg1 $body] }
```

Here the name of the lambda function created is the same as the command in which the lambda is invoked.

In general, ```K``` allows one to tag side effects onto computations in such a way as to sometimes avoid extra allocations or command invocations. As such, it can be useful for optimizing mission-critical code.

Do I recommend actually using it? Probably not.
