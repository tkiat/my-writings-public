# Purescript Proof: lift2 f x y = f <$> x <\*> y

I couldn't follow this statement (as in the title above) from the [Purescript by Example Chapter 7](https://book.purescript.org/chapter7.html) so with some effort I derived the proof as follow.

## The task

Given the definition of lift2 in the module [Control.Apply](https://pursuit.purescript.org/packages/purescript-prelude/5.0.1/docs/Control.Apply)

```haskell
lift2 :: forall a b c f.
Apply f => (a -> b -> c) -> f a -> f b -> f c
```

Proof that

```haskell
lift2 f x y = f \<$\> x \<\*\> y
```

## The Proof

### Problem 1: How do we convert a function g with type a -> b into type f a -> f b?

Ans. map

map definition,

```haskell
class Functor f where
map :: forall a b. (a -> b) -> f a -> f b
```

In other words, (map g) x = y, where x :: f a, y :: f b, and f is Functor

### Problem 2. How do we convert a function g with type a -> b -> c into type f a -> f b -> f c?

a -> b -> c is the same as a -> (b -> c) because of right-associativity.

Which is just a function accepting one argument (a), just like function g in problem 2. We can then use map directly to convert g to type f a -> f (b -> c)

Now we reach f a -> f (b -> c), but our goal is to get f a -> f b -> f c. A map is not enough. We can accomplish this when f is also an Applicative Functor (a more strict version of Functor).

Definition of Applicative Functor

```haskell
class Functor f <= Apply f where
apply :: forall b c. f (b -> c) -> f b -> f c
```

Upon using 'apply' function, we can convert f (b -> c) into f b -> f c. Now we reach f a -> f b -> f c.

I would like to reiterate everything. Given x :: f a, y :: f b, z :: f c, f is Applicative Functor. We convert g with type a -> b -> c into f a -> f b -> f c by

1. Make use of right associative property
   g is a -> (b -> c)
2. Use map
   map g is f a -> f (b -> c)
3. Take x
   (map g) x is f (b -> c)
4. Use apply
   apply ((map g) x) is f b -> f c
5. Take y
   apply ((map g) x) y is z

From 5 steps above, we use map, then take x, then use apply, then take y to convert g from a -> b -> c to f a -> f b -> f c, i.e. performing lift2.

In other words,

```haskell
lift2 g x y = apply ((map g) x) y.

lift2 g x y = g \<$\> x \<\*\> y
```

## Bonus

We can extend the previous proof indefinitely

```haskell
g \<$\> x \<\*\> y \<\*\> z \<\*\> z1 \<\*\> z2 ...
```

I take this for granted. Don't want to prove anymore.
