## Command line

*   Feed Dhall expressions to `dhall`'s standard input to type-check and evaluate them:

    ```bash
    $ dhall <<< 'True && False'
    ```
    ```haskell
    Bool

    False
    ```

*   Add the `--explain` flag for detailed explanations of type errors.

*   Add the `--pretty` flag to format output.

## Primitive types

*   `Bool`:

    ```haskell
    True, False : Bool

    True || False = True

    True && False = False

    True == False = False

    True != False = True

    if True then 1 else 2 = 1
    ```

*   `Natural`:

    A non-negative number (unsigned):

    ```haskell
    0, 1, 2, … : Natural

    2 + 3 = 5

    2 * 3 = 6

    Natural/build (λ(natural : Type) → λ(succ : natural → natural) → λ(zero : natural) → succ (succ (succ (succ zero)))) = 4

    Natural/fold 10 Text (λ(t : Text) → t ++ "!") "Hello" = "Hello!!!!!!!!!!"

    Natural/isZero 2 = False

    Natural/even 2 = True

    Natural/odd 2 = False

    Natural/show 2 = "2"
    ```

*   `Integer`:

    An integer, prefixed with a `+` or `-` sign:

    ```haskell
    …, -2, -1, +0, +1, +2, … : Integer

    Integer/show +2 = "+2"
    ```

*   `Double`:

    A double-precision floating point number with optional scientific notation:

    ```haskell
    -2.0, 3.14159, 1e10 : Double

    Double/show 2.0 = "2.0"
    ```

*   `Text`:

    ```haskell
    "", "Hello, world!", "☺", "  \u03bb(x : Type)\n\u2192 x" : Text

    ''
      Multi-line
      string
    '' = "Multi-line\nstring\n"

    "Interpolation: ${Natural/show 2}" = "Interpolation: 2"

    "ABC" ++ "DEF" = "ABCDEF"
    ```

## Complex types

*   `List`:

    A collection of 0 or more elements of the same type

    Type annotation is mandatory for empty lists:

    ```haskell
    [] : List Natural, [ 1, 2, 3 ]

    [ 1, 2, 3 ] # [ 4, 5, 6 ] = [ 1, 2, 3, 4, 5, 6 ]

    List/build Natural (λ(list : Type) → λ(cons : Natural → list → list) → λ(nil : list) → cons 1 (cons 2 (cons 3 nil))) = [ 1, 2, 3 ] : List Natural

    List/fold Bool [ True, False, True ] Natural (λ(x : Bool) → λ(y : Natural) → if x then y + 1 else y) 0 = 2

    List/length Natural [ 2, 3, 5 ] = 3

    List/head Natural [ 2, 3, 5 ] = [ 2 ] : Optional Natural

    List/last Natural [ 2, 3, 5 ] = [ 5 ] : Optional Natural

    List/indexed Natural [ 2, 3, 5 ] = [ { index = 0, value = 2 }, { index = 1, value = 3 }, { index = 2, value = 5 } ]

    List/reverse Natural [ 2, 3, 5 ] = [ 5, 3, 2 ]
    ```

*   `Optional`:

    Like a list, except at most one element (i.e. an `Optional` element)

    Type annotation is always mandatory:

    ```haskell
    [] : Optional Natural, [1] : Optional Natural

    
    Optional/fold Natural ([ 2 ] : Optional Natural) Text Natural/show "" = "2"

    Optional/build Natural (λ(optional : Type) → λ(just : Natural → optional) → λ(nothing : optional) → just 1) = [ 1 ] : Optional Natural
    ```

*   Records

    A mapping from field names to values that can be different types

    ```haskell
    {=} : {}  -- Empty record value requires an `=` to distinguish it from empty record type

    { foo = 1, bar = True } : { foo : Natural, bar : Bool }

    { foo = 1, bar = True }.foo = 1

    { foo = { bar = True } } ∧ { foo = { baz = "Hi" } } = { foo = { bar = True, baz = "Hi" } }

    { foo = 1, bar = True } ⫽ { bar = False } = { foo = 1, bar = False }
    ```

*   Unions

    ```haskell
    < Left = True | Right : Natural >, < Left : Bool | Right = 1 > : < Left : True | Right : Natural>

    merge { Left = λ(x : Bool) → x, Right = Natural/even } < Left : Bool | Right = 1 > = False
    ```

## Programming

*   `let` expressions:
  
    ```haskell
       let x = True

    in let y = False

    in x && y
    ```

    You can also use `let` expressions to name functions and imported values:

    ```haskell
        let not = λ(x : Bool) → x == False

    in  let show = http://prelude.dhall-lang.org/Bool/show

    in  show (not False)
    ```

*   Anonymous functions

    The type of a function's input argument is required and not inferred:

    ```haskell
    \(inputArgument : inputType) -> outputResult : forall (inputArgument : inputType) -> outputType  -- ASCII syntax

    λ(inputArgument : inputType) → outputResult : ∀(inputArgument : inputType) → outputType  -- Unicode syntax

        let describe =
            λ(name : Text)
          → λ(age : Natural)
          → "Name: ${name}, Age: ${Natural/show age}"

    in  describe "John Doe" 21 = "Name: John Doe, Age: 21"
    ```

*   Polymorphism

    Type abstraction and type application are explicit:

    ```haskell
    let id = λ(a : Type) → λ(x : a) → x in id Natural 4 = 4
    ```

*   Imports

    Imported paths or URLs are substituted for their contents:

    ```haskell
    [ ./you/can/import/paths, http://example.com/you/can/import/urls ] : ./even/for/types
    ```

*   Prelude

    You can find latest Prelude of importable functions at http://prelude.dhall-lang.org/