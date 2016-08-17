---
title: Enforcing restricted APIs at the type level using free monads
---

As a simple motivating example, suppose you wish to create an abstraction to model a retryable computation, that is, a computation which might fail and can be safely retried on failure. Such a computation must be pure to some extent -- if it has any nontrivial side effects, then there's no guarantee it can be safely retried.

For instance, it might be acceptable for a retryable action to read state from a database, but not to write to or update any data in the database. But database APIs do not typically expose a "read-only" version. In fact, these APIs are usually built on top of the monolithic `IO` monad, meaning that the power for side effects is theoretically and practically unlimited.

There are several ways in Haskell to create such a read-only API yourself. The approach detailed here uses free monads to define a read-only DSL; effects in the DSL can then be interpreted into actions in the full monad and executed in `IO`. As an example, I'll create a limited read-only API to Redis, built on top of the `hedis` library.

To begin with, we need to define the possible read-only actions available in our DSL. Before we do that, I should tell you that `hedis` functions use some clever type-level tricks in order to have "two possible types" depending on context. For instance, the ```get``` function returns a value of type either `Redis (Either Result ByteString)` or `Queued ByteString`. The former type is returned outside of transactions and the latter inside transactions (via `multiExec`). The ```Queued``` type is used to statically enforce that Redis query results cannot be used in the middle of a transaction but only after the entire transaction has been atomically processed.

As a simplification, we're only going to treat the former case, so our read-only API will only be usable outside of transactions. With that in mind, we can define the available read-only actions using an algebraic data type:

```haskell
type ReadOnlyResult a = Either Reply a

data ReadOnlyRedisF next =
    RedisGet    ByteString (ReadOnlyResult (Maybe ByteString) -> next)
    RedisExists ByteString (ReadOnlyResult Bool -> next)
    -- ...
    deriving (Functor)
```

The type parameter ```next``` specifies the type of the "next" computation in the monad. Intuitively, you can think of a `RedisGet` action as a combination of a key to look up (the first `ByteString` argument) and a continuation function which looks at the result of the `get` command and produces a follow-up computation.

With this definition, constructing the monad itself is trivial using ```Free```:

```haskell
type ReadOnlyRedis = Free ReadOnlyRedisF
```

We will leave the category-theoretic details of free monads for another time. Intuitively, a free object is one which has no structure beyond that implied directly by the axioms defining the object. In this case, a free monad has no interesting structure other than grafting monadic values together in the most trivial way possible while still satisfying the definition. Regardless of whether that really makes sense to you, using the result is easy.

We proceed by defining some helper functions to define actions directly in the ```ReadOnlyRedis``` monad:

```haskell
readOnlyGet :: ByteString -> ReadOnlyRedis (ReadOnlyResult (Maybe ByteString))
readOnlyGet key = liftF (RedisGet key id)

readOnlyExists :: ByteString -> ReadOnlyRedis (ReadOnlyResult Bool)
readOnlyExists key = liftF (RedisExists key id)
```

Finally, we need a function to interpret read-only actions into full actions in the `Redis` monad:

```haskell
promoteReadOnly :: ReadOnlyRedis a -> Redis a
promoteReadOnly =
    iterM (\op ->
              case op of
                  RedisGet    key rest -> get    key >>= rest
                  RedisExists key rest -> exists key >>= rest)
```

We might define another helper function to lift a read-only action straight into `IO` by running it:

```haskell
runReadOnly :: Connection -> ReadOnlyRedis a -> IO a
runReadOnly conn = runRedis conn . promoteReadOnly
```

Now we can write something like this:

```haskell
hw <- runReadOnly conn $ do
    hello <- readOnlyGet "hello"
    world <- readOnlyGet "world"
    return (hello, world)

print hw
```

But if we attempt to perform any effectful Redis queries, the program won't compile. We can't even get away with using `liftIO` or similar functions because there is no `IO` layer in the stack here. 

In summary, we've defined a read-only DSL on top of the `hedis` library which allows us to statically enforce that certain Redis actions only perform queries that we've deemed to not have side effects, like `get` or `exists`. There are many others that could be added to this interface for hashes, sets, sorted sets, hyperloglogs, etc. The most likely use case would be to construct a larger monad transfomer stack for retryable actions with `ReadOnlyRedis` sitting at the bottom-most layer.

