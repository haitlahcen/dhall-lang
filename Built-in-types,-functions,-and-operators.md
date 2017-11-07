This section describes all of the types, functions, and operators built into the
Dhall language.

* [`Bool`](#bool)
    * [Literals](#literals-bool)
    * [`if`/`then`/`else`](#construct-ifthenelse)
    * [`||`](#operator-)
    * [`&&`](#operator--1)
    * [`==`](#operator--2)
    * [`!=`](#operator--3)
* [`Natural`](#natural)
    * [Literals](#literals-natural)
    * [`+`](#operator--4)
    * [`*`](#operator--5)
    * [`Natural/even`](#function-naturaleven)
    * [`Natural/odd`](#function-naturalodd)
    * [`Natural/isZero`](#function-naturaliszero)
    * [`Natural/fold`](#function-naturalfold)
    * [`Natural/build`](#function-naturalbuild)
* [`Integer`](#integer)
    * [Literals](#literals-integer)
* [`Double`](#double)
    * [Literals](#literals-double)
* [`Text`](#text)
    * [Literals](#literals-text)
    * [`++`](#operator--6)
* [`List`](#list)
    * [Literals](#literals-list)
    * [`#`](#operator--7)
    * [`List/fold`](#function-listfold)
    * [`List/build`](#function-listbuild)
    * [`List/length`](#function-listlength)
    * [`List/head`](#function-listhead)
    * [`List/last`](#function-listlast)
    * [`List/indexed`](#function-listindexed)
    * [`List/reverse`](#function-listreverse)
* [`Optional`](#optional)
    * [`Optional`](#literals-optional)
    * [`Optional/fold`](#function-optionalfold)
    * [`Optional/build`](#function-optionalbuild)

# `Bool`

### Example

```bash
$ dhall <<< 'Bool'
```
```haskell
Type

Bool
```

### Type

```
────────────────
Γ ⊢ Bool : Type
```

## Literals: `Bool`

### Example

```bash
$ dhall <<< 'True'
```
```haskell
Bool

True
```

### Type

```
───────────────
Γ ⊢ True : Bool
```

```
────────────────
Γ ⊢ False : Bool
```

## Construct: `if`/`then`/`else`

### Example

```bash
$ dhall <<< 'if True then 3 else 5'
```
```haskell
Integer

3
```

### Type

```
               Γ ⊢ t : Type
               ─────────────────────
Γ ⊢ b : Bool   Γ ⊢ l : t   Γ ⊢ r : t
────────────────────────────────────
Γ ⊢ if b then l else r : t
```

### Rules

```haskell
if b then True else False = b

if True  then l else r = l

if False then l else r = r
```

## Operator: `||`

### Example

```bash
$ dhall <<< 'True || False'
```
```haskell
Bool

True
```

### Type

```
Γ ⊢ x : Bool   Γ ⊢ y : Bool
───────────────────────────
Γ ⊢ x || y : Bool
```

### Rules

```haskell
x || False = x

False || x = x

(x || y) || z = x || (y || z)

x || True = True

True || x = True

x || (y && z) = (x || y) && (x || z)

(x && y) || z = (x || z) && (y || z)
```

## Operator: `&&`

### Example

```bash
$ dhall <<< 'True && False'
```
```haskell
Bool

False
```

### Type

```
Γ ⊢ x : Bool   Γ ⊢ y : Bool
───────────────────────────
Γ ⊢ x && y : Bool
```

### Rules

```haskell
x && True = x

True && x = x

(x && y) && z = x && (y && z)

x && False = False

False && x = False

x && (y || z) = (x && y) || (x && z)

(x || y) && z = (x && z) || (y && z)
```

## Operator: `==`

### Example

```bash
$ dhall <<< 'True == False'
```
```haskell
Bool

False
```

### Type

```
Γ ⊢ x : Bool   Γ ⊢ y : Bool
───────────────────────────
Γ ⊢ x == y : Bool
```

### Rules

```haskell
x == x = True

x == True = x

True == x = x

(x == y) == z = x == (y == z)
```

## Operator: `!=`

### Example

```bash
$ dhall <<< 'True != False'
```
```haskell
Bool

True
```

### Type

```
Γ ⊢ x : Bool   Γ ⊢ y : Bool
───────────────────────────
Γ ⊢ x != y : Bool
```

### Rules

```haskell
x != x = False

False != x = x

x != False = x

(x != y) != z = x != (y != z)
```

# `Natural`

### Example

```bash
$ dhall <<< 'Natural'
```
```haskell
Type

Natural
```

### Type

```
Natural : Type
```

## Literals: `Natural`

A `Natural` number literal is a non-negative integer prefixed with a `+` sign.

### Example

```bash
$ dhall <<< '+2'
```
```haskell
Natural

+2
```

### Type

```
────────────────
Γ ⊢ +n : Natural
```

### Rules

```haskell
+n = +1 + +1 + … + +1 + +1  -- n times
```

## Operator: `+`

### Example

```bash
$ dhall <<< '+2 + +3'
```
```haskell
Natural

+5
```

### Type

```
Γ ⊢ x : Natural   Γ ⊢ y : Natural
─────────────────────────────────
Γ ⊢ x + y : Natural
```

### Rules

```haskell
x + +0 = x

+0 + x = x

(x + y) + z = x + (y + z)
```

## Operator: `*`

### Example

```bash
$ dhall <<< '+2 * +3'
```
```haskell
Natural

+6
```

### Type

```
Γ ⊢ x : Natural   Γ ⊢ y : Natural
─────────────────────────────────
Γ ⊢ x * y : Natural
```

### Rules

```haskell
x * +1 = x

+1 * x = x

(x * y) * z = x * (y * z)

x * +0 = +0

+0 * x = +0

(x + y) * z = (x * z) + (y * z)

x * (y + z) = (x * y) + (x * z)
```

## Function: `Natural/even`

### Example

```bash
$ dhall <<< 'Natural/even +6'
```
```haskell
Bool

True
```

### Type

```
─────────────────────────────────
Γ ⊢ Natural/even : Natural → Bool
```

### Rules

```haskell
Natural/even +0 = True

Natural/even (x + y) = Natural/even x == Natural/even y

Natural/even +1 = False

Natural/even (x * y) = Natural/even x || Natural/even y
```

## Function: `Natural/odd`

### Example

```bash
$ dhall <<< 'Natural/odd +6'
```
```haskell
Bool

False
```

### Type

```
────────────────────────────────
Γ ⊢ Natural/odd : Natural → Bool
```

### Rules

```haskell
Natural/odd +0 = False

Natural/odd (x + y) = Natural/odd x != Natural/odd y

Natural/odd +1 = True

Natural/odd (x * y) = Natural/odd x && Natural/odd y
```

## Function: `Natural/isZero`

### Example

```bash
$ dhall <<< 'Natural/isZero + 6'
```
```haskell
Bool

False
```

### Type

```
───────────────────────────────────
Γ ⊢ Natural/isZero : Natural → Bool
```

### Rules

```haskell
Natural/isZero +0 = True

Natural/isZero (x + y) = Natural/isZero x && Natural/isZero y

Natural/isZero +1 = False

Natural/isZero (x * y) = Natural/isZero x || Natural/isZero y
```

## Function: `Natural/fold`

### Example

```bash
$ dhall <<< 'Natural/fold +40 Text (λ(t : Text) → t ++ "!") "You're welcome"'
```
```haskell
Text

"You're welcome!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
```

### Type

```
──────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ Natural/fold : Natural → ∀(natural : Type) → ∀(succ : natural → natural) → ∀(zero : natural) → natural
```

### Rules

```haskell
Natural/fold +0 n s z = z

Natural/fold (x + y) n s z = Natural/fold x n s (Natural/fold y n s z)

Natural/fold +1 n s = s

Natural/fold (x * y) n s = Natural/fold x n (Natural/fold y n s)
```

## Function: `Natural/build`

### Example

```bash
$ dhall <<< 'Natural/build (λ(natural : Type) → λ(succ : natural → natural) → λ(zero : natural) → succ (succ zero))'
```
```haskell
Natural

+2
```

### Type

```
─────────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ Natural/build : (∀(natural : Type) → ∀(succ : natural → natural) → ∀(zero : natural) → natural) → Natural
```

### Rules

```haskell
Natural/fold (Natural/build x) = x

Natural/build (Natural/fold x) = x
```

# `Integer`

### Example

```bash
$ dhall <<< 'Integer'
```
```haskell
Type

Integer
```

### Type

```
────────────────
Γ ⊢ Integer : Type
```

## Literals: `Integer`

An `Integer` literal is a either a non-negative integer (without a `+` sign) or
a negative integer prefixed with a `-`.

### Examples

```bash
$ dhall <<< '3'
```
```haskell
Integer

3
```

```bash
$ dhall <<< '-2'
```
```haskell
Integer

-2
```

### Type

```
────────────────
Γ ⊢ n : Integer
```

# `Double`

### Example

```bash
$ dhall <<< 'Double'
```
```haskell
Type

Double
```

### Type

```
────────────────
Γ ⊢ Double : Type
```

## Literals: `Double`

A `Double` literal must have either at least one decimal place or an exponent
(or both):

### Examples

```bash
$ dhall <<< '3.14159'
```
```haskell
Double

3.14159
```

```bash
$ dhall <<< '-2e10'
```
```haskell
Double

-2.0e10
```

### Type

```
────────────────
Γ ⊢ n.n : Double
```

# `Text`

### Example

```bash
$ dhall <<< 'Text'
```
```haskell
Type

Text
```

### Type

```
────────────────
Γ ⊢ Text : Type
```

## Literals: `Text`

A `Text` literal is either a double-quoted string literal with JSON-style
escaping rules or a Nix-style multi-line string literal:

### Examples

```bash
$ dhall <<< '"ABC"'
```
```haskell
Text

"ABC"
```

```bash
$ dhall <<EOF
> ''
>     Line 1
>     Line 2
> ''
> EOF
```
```haskell
Text

"Line 1\nLine 2\n"
```

### Type

```
────────────────
Γ ⊢ "…" : Text
```

### Rules

```haskell
"abc…xyz" = "a" ++ "b" ++ "c" ++ … ++ "x" ++ "y" ++ "z"
```

## Operator: `++`

### Example

```bash
$ dhall <<< '"Hello, " ++ "world!"'
```
```haskell
Text

"Hello, world!"
```

### Type

```
Γ ⊢ x : Text   Γ ⊢ y : Text
───────────────────────────
Γ ⊢ x && y : Text
```

### Rules

```haskell
(x ++ y) ++ z = x ++ (y ++ z)

x ++ "" = x

"" ++ x = x
```

# `List`

### Example

```bash
$ dhall <<< 'List'
```
```haskell
Type → Type

List
```

### Type

```
──────────────────────
Γ ⊢ List : Type → Type
```

## Literals: `List`

A `List` literal is a sequence of 0 or more comma-separated values inside
square brackets.

An empty `List` literal must end with a type annotation.

### Examples

```bash
$ dhall <<< '[ 1, 2, 3 ]'
```
```haskell
List Integer

[ 1, 2, 3 ]
```

```bash
dhall <<< '[] : List Integer'
```
```haskell
List Integer

[] : List Integer
```

### Type

```
Γ ⊢ t : Type   Γ ⊢ x : t   Γ ⊢ y : t   …
────────────────────────────────────────
Γ ⊢ [x, y, … ] : List t
```

### Rules

```haskell
[ a, b, c, …, x, y, z ] = [ a ] # [ b ] # [ c ] # … # [ x ] # [ y ] # [ z ]
```

## Operator: `#`

### Example

```bash
$ dhall <<< '[ 1, 2, 3] # [ 4, 5, 6 ]'
```
```haskell
List Integer

[ 1, 2, 3, 4, 5, 6, ]
```

### Type

```
Γ ⊢ x : List a    Γ ⊢ y : List a
─────────────────────────────────
Γ ⊢ x # y : List a
```

### Rules

```haskell
([] : List a) # xs = xs

xs # ([] : List a) = xs

(xs # ys) # zs = xs # (ys # zs)
```

## Function: `List/fold`

### Example

```bash
$ dhall <<< 'List/fold Bool [True, False, True] Bool (λ(x : Bool) → λ(y : Bool) → x && y) True'
```
```haskell
Bool

False
```

### Type

```
────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ List/fold : ∀(a : Type) → List a → ∀(list : Type) → ∀(cons : a → list → list) → ∀(nil : list) → list
```

### Rules

```haskell
List/fold a ([] : List a) b c n = n

List/fold a (xs # ys) b c n = List/fold a xs b c (List/fold ys b c n)

List/fold a ([x] : List a) b c = c x
```

## Function: `List/build`

### Example

```bash
$ dhall <<< 'List/build Integer (λ(list : Type) → λ(cons : Integer → list → list) → λ(nil : list) → cons 1 (cons 2 (cons 3 nil)))'
```
```haskell
List Integer

[1, 2, 3] : List Integer
```

### Type

```
───────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ List/build : ∀(a : Type) → (∀(list : Type) → ∀(cons : a → list → list) → ∀(nil : list) → list) → List a
```

### Rules

```haskell
List/build t (List/fold t x) = x

List/fold t (List/build t x) = x
```

## Function: `List/length`

### Example

```bash
$ dhall <<< 'List/length Integer [ 1, 2, 3 ]'
```
```haskell
Natural

+3
```

### Type

```
────────────────────────────────────────────────
Γ ⊢ List/length : ∀(a : Type) → List a → Natural
```

### Rules

```haskell
List/length t ([] : List t) = +0

List/length t (xs # ys) = List/length t xs + List/length t ys

List/length t [ x ] = +1
```

### Function: `List/head`

```bash
$ dhall <<< 'List/head Integer [ 1, 2, 3 ]'
```
```haskell
Optional Integer

[ 1 ] : Optional Integer
```

### Type

```
───────────────────────────────────────────────
Γ ⊢ List/head ∀(a : Type) → List a → Optional a
```

### Rules

```haskell
List/head a ([] : List a) = [] : Optional a

List/head a (xs # ys) =
      let combine =
              λ(a : Type)
            → λ(l : Optional a)
            → λ(r : Optional a)
            → Optional/fold a l (Optional a) (λ(x : a) → [ x ] : Optional a) r
  in  combine a (List/head a xs) (List/head a ys)

List/head a [ x ] = [ x ] : Optional a
```

## Function: `List/last`

### Example

```bash
$ dhall <<< 'List/last Integer [ 1, 2, 3 ]'
```
```haskell
Optional Integer

[ 3 ] : Optional Integer
```

### Type

```
─────────────────────────────────────────────────
Γ ⊢ List/last : ∀(a : Type) → List a → Optional a
```

### Rules

```haskell
List/last a ([] : List a) = [] : Optional a

List/last a (xs # ys) =
      let combine =
              λ(a : Type)
            → λ(l : Optional a)
            → λ(r : Optional a)
            → Optional/fold a r (Optional a) (λ(x : a) → [ x ] : Optional a) l
  in  combine a (List/last a xs) (List/last a ys)

List/last a [ x ] = [ x ] : Optional a
```

## Function: `List/indexed`

### Example

```bash
$ dhall <<< 'List/indexed Text [ "ABC", "DEF", "GHI" ]'
```
```haskell
List { index : Natural, value : Text }

[{ index = +0, value = "ABC" }, { index = +1, value = "DEF" }, { index = +2, value = "GHI" }] : List { index : Natural, value : Text }
```

### Type

```
─────────────────────────────────────────────────────────────────────────────
Γ ⊢ List/indexed : ∀(a : Type) → List a → List { index : Natural, value : a }
```

### Rules

```haskell
List/indexed a ([] : List a) = [] : List { index : Natural, value : a }

List/indexed a (xs # ys) =
      let combine =
          λ(a : Type)
        → λ(xs : List { index : Natural, value : a })
        → λ(ys : List { index : Natural, value : a })
        →   xs
          # List/build
            { index : Natural, value : a }
            (   λ(list : Type)
              → λ(cons : { index : Natural, value : a } → list → list)
              → List/fold
                { index : Natural, value : a }
                ys
                list
                (   λ(x : { index : Natural, value : a })
                  → cons
                    { index = x.index + List/length { index : Natural, value : a } xs
                    , value = x.value
                    }
                )
            )
  in  combine a (List/indexed a xs) (List/indexed a ys)

List/indexed a [ x ] = [ { index = +0, value = x } ]
```

## Function: `List/reverse`

### Example

```bash
$ dhall <<< 'List/reverse Integer [ 1, 2, 3 ]'
```
```haskell
List Integer

[ 3, 2, 1 ] : List Integer
```

### Type

```
─────────────────────────────────────────────────
Γ ⊢ List/reverse : ∀(a : Type) → List a → List a
```

### Rules

```haskell
List/reverse a ([] : List a) = [] : List a

List/reverse a [ x ] = [ x ]

List/reverse a (xs # ys) = List/reverse a ys # List/reverse a xs
```

# `Optional`

### Example

```bash
$ dhall <<< 'Optional'
```
```haskell
Type → Type

Optional
```

### Type

```
──────────────────────────
Γ ⊢ Optional : Type → Type
```

## Literals: `Optional`

An `Optional` literal is either 0 or 1 values inside square brackets followed by
a type annotation

### Example

```bash
$ dhall <<< '[] : Optional Integer'
```
```haskell
Optional Integer

[] : Optional Integer
```

```bash
$ dhall <<< '[ 1 ] : Optional Integer'
```
```haskell
Optional Integer

[ 1 ] : Optional Integer
```

### Type

```
Γ ⊢ t : Type   Γ ⊢ x : t
───────────────────────────────────
Γ ⊢ ([x] : Optional t) : Optional t
```

```
Γ ⊢ t : Type
──────────────────────────────────
Γ ⊢ ([] : Optional t) : Optional t
```

## Function: `Optional/fold`

### Example

```bash
$ dhall <<< 'Optional/fold Text (["ABC"] : Optional Text) Text (λ(t : Text) → t) ""'
```
```haskell
Text

"ABC"
```

### Type

```
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ Optional/fold : ∀(a : Type) → Optional a → ∀(optional : Type) → ∀(just : a → optional) → ∀(nothing : optional) → optional
```

### Rules

```haskell
Optional/fold a ([]  : Optional a) o j n = n

Optional/fold a ([x] : Optional a) o j n = j x
```

## Function: `Optional/build`

### Example

```bash
$ dhall <<< 'Optional/build Text (λ(optional : Type) → λ(just : Text → optional) → λ(nothing : optional) → just "abc")'
```
```haskell
Optional Text

[ "abc" ] : Optional Text
```

### Type

```
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
Γ ⊢ Optional/build : ∀(a : Type) → (∀(optional : Type) → ∀(just : a → optional) → ∀(nothing : optional) → optional) → Optional a
```

### Rules

```haskell
Optional/build t (Optional/fold t x) = x

Optional/fold t (Optional/build t x) = x
```
