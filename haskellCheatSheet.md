### `forall` keyword ([link](https://wasp-lang.dev/blog/2021/09/01/haskell-forall-tutorial))
1. Implicitly, all type signatures are universally quantified. `f :: a -> a` is actually `f :: forall a. a -> a`. 
This can be made explicit using the `ExplicitForAll` extension.
2. `forall` allows scoping of type variables over the entire type signature and definition of a function with the `ScopedTypeVariables` extension.
This won't compile:
```
f :: [a] -> [a]
f xs = ys ++ ys
  where ys :: [a]
        ys = reverse xs
```
, but this will:
```
{-# LANGUAGE ScopedTypeVariables #-}

f :: forall a. [a] -> [a]
f xs = ys ++ ys
  where ys :: [a]
        ys = reverse xs
```
3. `forall` can be nested within type signatures with the `RankNTypes` extension.
For example, `foo` requires its first argument to be a polymorphic function, some specific identity function won't do.
```
foo :: (forall a. a -> a) -> (Char, Bool)
```
4. `forall` can be used in teh type signature of data constructors using the `ExistentialQuantification` extension.
For example,
```
data Showable = forall s. (Show s) => Showable s

someShowables :: [Showable]
someShowables = [Showable "Hi", Showable 5, Showable (1, 2)]

printShowables :: [Showable] -> IO ()
printShowables ss = mapM_ (\(Showable s) -> print s) ss
```

### Record Syntax ([link](http://learnyouahaskell.com/making-our-own-types-and-typeclasses#record-syntax))
`data Person = Person { firstname :: String, lastName :: String}` is syntactive sugar for 
```
data Person = Person String String

firstName :: Person -> String
firstName = (Person f _) = f

lastName :: Person -> String
lastName = (Person _ l) = l
```
