You can use the Haskell API to extend the Dhall configuration language with new
built-in functions.  This section contains a simple Haskell recipe to add a
new `Natural/equal` built-in function of type:

```haskell
Natural/equal : Natural → Natural → Bool
```

To do so, we:

* extend the type-checking context to include the type of `Natural/equal`
* extend the normalizer to evaluate all occurrences of `Natural/equal`

... like this:

```haskell
-- example.hs

{-# LANGUAGE OverloadedStrings #-}

module Main where

import Dhall.Core (Expr(..), ReifiedNormalizer(..))

import qualified Data.Text.IO
import qualified Dhall
import qualified Dhall.Context
import qualified Lens.Family   as Lens

main :: IO ()
main = do
    text <- Data.Text.IO.getContents

    let startingContext = transform Dhall.Context.empty
          where
            transform = Dhall.Context.insert "Natural/equal" naturalEqualType

            naturalEqualType =
                Pi "_" Natural (Pi "_" Natural Bool)

    let normalizer (App (App (Var "Natural/equal") (NaturalLit x)) (NaturalLit y)) =
            Just (BoolLit (x == y))
        normalizer _ =
            Nothing

    let inputSettings = transform Dhall.defaultInputSettings
          where
            transform =
                  Lens.set Dhall.normalizer      (ReifiedNormalizer normalizer)
                . Lens.set Dhall.startingContext startingContext

    x <- Dhall.inputWithSettings inputSettings Dhall.auto text

    Data.Text.IO.putStrLn x
```

Here is an example use of the above program:

```bash
$ ./example <<< 'if Natural/equal 2 (1 + 1) then "Equal" else "Not equal"'
Equal
```

Note that existing Dhall tools that type-check expressions will reject
expressions containing unexpected free variable such as `Natural/equal`:

```bash
$ dhall <<< 'Natural/equal 2 (1 + 1)'

Use "dhall --explain" for detailed errors

Error: Unbound variable

Natural/equal 

(stdin):1:1
```

You will need to either:

* create your own parallel versions of these tools, or:
* [try to upstream your built-ins into the language](https://github.com/dhall-lang/dhall-lang/blob/master/.github/CONTRIBUTING.md#how-do-i-change-the-language)