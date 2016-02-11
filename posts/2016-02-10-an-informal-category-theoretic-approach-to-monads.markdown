---
title: An informal category-theoretic approach to monads
---

This short post is intended for those who would like to understand monads from a category-theoretic perspective but who don't want to be overwhelmed with too many technicalities. To achieve this brief presentation, I've chosen to forgo various technical definitions which I feel are unnecessary at the level of pure intuition, and I've also chosen to use an alternate formulation of the monad axioms which lends itself to a more elegant description here but deviates from the standard Haskell typeclass. The result of course is completely equivalent to the definition used explicitly in Haskell, but this equivalence must be demonstrated and may not be obvious to the reader.

In Haskell, one typically deals only with a single category, the category of types. This is commonly called `Hask`.

Keep in mind that `Hask` is not just a collection (or class) of types. (It cannot be a set of types due to Russell's paradox.) It also contains morphisms between types. These are just functions.

A functor is sort of like a function between two categories. A functor from a category to itself is an endofunctor: scary name, simple concept.

An endofunctor in Haskell is basically a type constructor: a function that takes a type and produces a new type. Note, though, that a functor doesn't just deal with the objects of the category (types in our case) but also with the morphisms. That's the job of `fmap` in Haskell. (So what is called a functor in Haskell parlance is perhaps more accurately called an endofunctor.)

A monad is an endofunctor -- again, just a `Functor` in Haskell -- along with two other special operations (called natural transformations, but let's not worry about that too much). Remember that the endofunctor here comes with a type constructor, say `T`. 

One of these operations, called *return* or *lift*, takes an object of type `x` and produces an object of type `T x`. That's simple enough. So, for instance, in the list monad this operation takes an object, say of type `Int`, and it produces an object of type `[Int]`. (Which object, specifically, might it produce? Of course, just the singleton list whose element is precisely the provided integer.)

The other special operation, called *join*, is a bit more interesting. It takes an object of type `T (T x)` and produces an object of the simpler type `T x` -- it flattens the two successive applications of the type constructor. Again, as with all this stuff, it's actually very simple. In the list monad, for instance, this join operation is just list concatenation -- it takes a list of lists and produces a single list.

*join* is typically the more interesting of the two operations since it has a greater opportunity to delve into the monad's unique structure. *return* on the other hand in a universal sense must always be as simple as possible since it knows nothing specific about the underlying type of the object `x` that it's operating on.

Anything that can be modeled this way -- be it a data structure, computation, API, or just a pattern of some sort -- is a monad. And that's really all there is to it. It turns out a whole lot of things can be modeled this way, so Haskell includes some syntactic sugar for working with this pattern (`do` notation).

From here, further study of monads should focus just on seeing how existing monads in Haskell fit this pattern. The more you look at concrete examples, the more straightforward this construction will seem.
