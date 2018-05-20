The Dhall configuration language only provides built-in support for one
recursive data type: `List`s.  However, the language does not provide native
support for user-defined recursive types, recursive values, or recursive
functions.

Despite that limitation, you can still transform recursive code into
non-recursive Dhall code.  This guide will explain how by example, walking
through examples of progressively increasing difficulty.

## Recursive record

Consider the following recursive Haskell code:

```haskell
-- Example0.hs

data Person = MakePerson
    { name     :: String
    , children :: [Person]
    }

example :: Person
example =
    MakePerson
    { name     = "John"
    , children =
        [ MakePerson { name = "Mary", children = [] }
        , MakePerson { name = "Jane", children = [] }
        ]
    }

everybody :: Person -> [String]
everybody p = name p : concatMap everybody (children p)

result :: [String]
result = everybody example
```

... which evaluates to:

```bash
$ ghci Example0.hs
*Main> result
["John","Mary","Jane"]
```

The equivalent Dhall code would be:

```haskell
-- example0.dhall

    let Person : Type =
            ∀(Person : Type)
          → ∀(MakePerson : { children : List Person, name : Text } → Person)
          → Person

in  let example : Person =
            λ(Person : Type)
          → λ(MakePerson : { children : List Person, name : Text } → Person)
          → MakePerson
            { children =
                [ MakePerson { children = [] : List Person, name = "Mary" }
                , MakePerson { children = [] : List Person, name = "Jane" }
                ]
            , name     = "John"
            }

in  let everybody : Person → List Text =
              let concat = http://prelude.dhall-lang.org/List/concat 
          
          in    λ(x : Person)
              → x
                (List Text)
                (   λ(p : { children : List (List Text), name : Text })
                  → [ p.name ] # concat Text p.children
                )

in  let result : List Text = everybody example

in  result
```

... which evaluates to the same result:

```bash
$ dhall <<< './example0.dhall'
List Text

[ "John", "Mary", "Jane" ]
```

Carefully note that there is more than one bound variable named `Person` in the
above example.  We can disambiguate them by prefixing some of them with an
underscore (i.e. `_Person`):

```haskell
    let Person : Type =
            ∀(_Person : Type)
          → ∀(MakePerson : { children : List _Person, name : Text } → _Person)
          → _Person

in  let example : Person =
            λ(_Person : Type)
          → λ(MakePerson : { children : List _Person, name : Text } → _Person)
          → MakePerson
            { children =
                [ MakePerson { children = [] : List _Person, name = "Mary" }
                , MakePerson { children = [] : List _Person, name = "Jane" }
                ]
            , name     = "John"
            }

in  let everybody : Person → List Text =
              let concat = http://prelude.dhall-lang.org/List/concat 
          
          in    λ(x : Person)
              → x
                (List Text)
                (   λ(p : { children : List (List Text), name : Text })
                  → [ p.name ] # concat Text p.children
                )

in  let result : List Text = everybody example

in  result
```

The way that this works is that a recursive function like `everybody` is
performing substitution.  In this specific case, `everybody` is:

*   replacing each occurrence of the type `Person` with the type `List Text`
*   replacing each occurrence of the `MakePerson` function with the following
    anonymous function:

    ```haskell
       λ(p : { children : List (List Text), name : Text })
    → [ p.name ] # concat Text p.children
    ```

... which means that our previous example could also have been written like
this:

```haskell
    let concat = http://prelude.dhall-lang.org/List/concat 

in  let Person : Type = List Text

in  let MakePerson
        : { children : List Person, name : Text } → Person
        =   λ(p : { children : List Person, name : Text })
          → [ p.name ] # concat Text p.children

in  let result =
          MakePerson
          { children =
              [ MakePerson { children = [] : List Person, name = "Mary" }
              , MakePerson { children = [] : List Person, name = "Jane" }
              ]
          , name     = "John"
          }

in  result
```

## Recursive sum type

Sum types work in the same way, except that instead of one constructor (i.e.
`MakePerson`) we now have two constructors: `Succ` and `Nat`.  For example, this
Haskell code:

```haskell
-- Example1.hs

import Numeric.Natural (Natural)

data Nat = Zero | Succ Nat

example :: Nat
example = Succ (Succ (Succ Zero))

toNatural :: Nat -> Natural
toNatural  Zero    = 0
toNatural (Succ n) = 1 + toNatural n

result :: Natural
result = toNatural example
```

... which produces this `result`:

```bash
$ ghci Example1.hs 
*Main> result
3
```

... corresponds to this Dhall code:

```haskell
-- example1.dhall

    let Nat : Type = ∀(Nat : Type) → ∀(Zero : Nat) → ∀(Succ : Nat → Nat) → Nat

in  let example
        : Nat
        =   λ(Nat : Type)
          → λ(Zero : Nat)
          → λ(Succ : Nat → Nat)
          → Succ (Succ (Succ Zero))

in  let toNatural
        : Nat → Natural
        = λ(x : Nat) → x Natural 0 (λ(n : Natural) → 1 + n)

in  let result : Natural = toNatural example

in  result
```

... which produces the same `result`:

```bash
$ dhall <<< './example1.dhall'
Natural

3
```

Like before, our recursive `toNatural` function is performing substitution by:

*   replacing every occurrence of `Nat` with `Natural`
*   replacing every occurrence of `Zero` with `0`
*   replacing every occurrence of `Succ` with an anonymous funciton

... which means that we could have equivalently written:

```haskell
    let Nat = Natural

in  let Zero : Nat = 0

in  let Succ : Nat → Nat = λ(n : Nat) → 1 + n

in  let result : Nat = Succ (Succ (Succ Zero))

in  result
```

## Mutually recursive types

The above pattern generalizes to mutually recursive types, too.  For example,
this Haskell code:

```haskell
-- Example2.hs

import Numeric.Natural

data Even = Zero | SuccEven Odd

data Odd = SuccOdd Even

example :: Odd
example = SuccOdd (SuccEven (SuccOdd Zero))

oddToNatural :: Odd -> Natural
oddToNatural (SuccOdd e) = 1 + evenToNatural e

evenToNatural :: Even -> Natural
evenToNatural  Zero        = 0
evenToNatural (SuccEven o) = 1 + oddToNatural o

result :: Natural
result = oddToNatural example
```

... which produces this `result`:

```bash
$ ghci Example2.hs 
*Main> result
3
```

... corresponds to this Dhall code:

```haskell
    let Odd
        : Type
        =   ∀(Even : Type)
          → ∀(Odd : Type)
          → ∀(Zero : Even)
          → ∀(SuccEven : Odd → Even)
          → ∀(SuccOdd : Even → Odd)
          → Odd

in  let example
        : Odd
        =   λ(Even : Type)
          → λ(Odd : Type)
          → λ(Zero : Even)
          → λ(SuccEven : Odd → Even)
          → λ(SuccOdd : Even → Odd)
          → SuccOdd (SuccEven (SuccOdd Zero))

in  let oddToNatural
        : Odd → Natural
        =   λ(o : Odd)
          → o
            Natural
            Natural
            0
            (λ(n : Natural) → 1 + n)
            (λ(n : Natural) → 1 + n)

in  let result = oddToNatural example

in  result
```

... which produces the same `result`:

```bash
$ dhall <<< './example2.dhall' 
Natural

3
```

The trick here is that the Dhall's `Odd` type combines both of the Haskell
`Even` and `Odd` types.  Similarly, Dhall's `oddToNatural` function combines
both of the Haskell `evenToNatural` and `oddToNatural` functions.  You can
define a separate `Even` and `evenToNatural` in Dhall, too, but they would not
reuse any of the logic from `Odd` or `oddToNatural`.

Like before, our recursive `oddToNatural` function is performing substitution
by:

*   replacing every occurrence of `Even` with `Natural`
*   replacing every occurrence of `Odd` with `Natural`
*   replacing every occurrence of `Zero` with `0`
*   replacing every occurrence of `SuccEven` with an anonymous function
*   replacing every occurrence of `SuccOdd` with an anonymous function

... which means that we could have equivalently written:

```haskell
    let Odd : Type = Natural

in  let Even : Type = Natural

in  let Zero : Even = 0

in  let SuccEven : Odd → Even = λ(n : Odd) → 1 + n

in  let SuccOdd : Even → Odd = λ(n : Even) → 1 + n

in  let result = SuccOdd (SuccEven (SuccOdd Zero))

in  result
```

## Conclusion

The general algorithm for translating recursive code to non-recursive code is
known as Boehm Berarducci encoding and based off of this paper:

* [Automatic synthesis of typed Λ-programs on term algebras](http://www.sciencedirect.com/science/article/pii/0304397585901355)

This guide doesn't explain the full algorithm due to the amount of detail
involved, but if you are interested you can read the above paper.

Also, if the above examples were not sufficient, feel free to open an issue to
request another example to add to this guide.
