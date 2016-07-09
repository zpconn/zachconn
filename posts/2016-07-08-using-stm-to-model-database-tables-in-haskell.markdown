---
title: Using STM to model database tables in Haskell
---

This post outlines a general technique for modeling transactional database tables purely in-memory using STM (software transactional memory) in Haskell.

The general idea is that we'd like a table column to be a transactional map associating integer row IDs with transactional values, leading to naive definitions like so:

```haskell
newtype IntColumn = IntColumn (TVar (Map Int (TVar Int)))
newtype NullableIntColumn = NullableIntColumn (TVar (Map Int (TVar (Maybe Int))))
...
```
We can then define a table as an algebraic data type quantified over its columns, along with perhaps a row counter of some sort:

```haskell
data ExampleTable = ExampleTable { rowCount :: Int
	                         , rowA :: NullableIntColumn
		                 , rowB :: FloatColumn
		                 ...
                                 }

```

Composable STM transactions can then modify this table and others. 

This naive approach has a whole host of problems, though. To begin with, we could use ```IntMap```, which is optimized for integer keys, instead of something like ```Map Int (TVar Int)``` for a bit of extra performance. More seriously, though, it quickly leads to an untenable amount of boilerplate. Moreover, it really doesn't make sense to model the columns separately at all. The above table would potentially allow the different columns to be out of sync. By getting rid of individual columns, it also becomes very easy to make the table type polymorphic:

```haskell
newtype Table a = Table (TVar (IntMap (TVar a)))
```

It's now very easy to define new table types with almost no boilerplate:

```haskell
data ExampleRow = ExampleRow { rowA :: Maybe Int
                             , rowB :: Float
                             }

newtype ExampleTable = Table ExampleRow
```

We can also write just a single function to create a new table of any type:

```haskell
createNewTable :: STM (Table a)
createNewTable = Table <$> IntMap.empty
```

This looks much cleaner, but there's a serious problem under the hood which may not be evident. The problem is that concurrent transactions trying to add new rows will conflict with one another and in fact all other transactions, thus leading to mass contention and lots of transaction retries. While ```IntMap``` is perfectly safe to use in STM, it's just not very performant. We'd be much better off using a transactional trie: [ttrie](https://hackage.haskell.org/package/ttrie). ```ttrie``` is a lock-free concurrent map lifted into STM with the special property that no contention occurs unless two transactions are explicitly operating on the same key. Thus, a transaction adding a new key to the map will not generally create any contention.

So we can amend our definitions like so:

```haskell
newtype Table a = Table (TVar (TTrie.Map Int a))
...
createNewTable :: STM (Table a)
createNewTable = Table <$> TTrie.empty
```

Indexes can be modeled as inverted tables mapping indexed values to sets of row IDs:

```haskell
newtype Index k = Index (TVar (TTrie.Map k (HashSet Int)))
```

Queries of course must be implemented manually, but the general technique for simple queries is to perform several index lookups, yielding several sets of row IDs. The query result is then the intersection of these sets.

`ttrie` scales phenomenally and handles direct contention fairly well too, so this technique, when brought to bear against the right problem, can yield world-class performance and concurrency for programs that need transactional semantics on a few small in-memory tables, but don't need a full relational database, durability, JOINs, or other complex adhoc queries.

