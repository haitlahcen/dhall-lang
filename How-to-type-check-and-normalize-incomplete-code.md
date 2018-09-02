You can type-check and normalize incomplete code by introducing a function argument named
`TODO` that has the following type:

```haskell
λ(TODO : ∀(a : Type) → a) → …
```

Then you can use `TODO` to fill any gaps in your code that have not been
implemented yet:

```haskell
  λ(TODO : ∀(a : Type) → a)
→     let List/map =
            https://raw.githubusercontent.com/dhall-lang/Prelude/302881a17491f3c72238975a6c3e7aab603b9a96/List/map sha256:fe612cecad4dc5ef3f884dcd705e6747dd0e977109c5fbe5cee925d8f5ad6f4b
  
  in  let Text/concatSep =
            https://raw.githubusercontent.com/dhall-lang/Prelude/302881a17491f3c72238975a6c3e7aab603b9a96/Text/concatSep sha256:271078081f5b336a1c3e47c3104736b1ec82043e9b571c80f6030a9556869e26
  
  in  let Person = { name : Text, age : Natural }
  
      --  ↓ You can leave functions unimplemented
  in  let renderPerson = TODO (Person → Text)
  
  in  let renderList =
            λ(elements : List Text) → "[" ++ Text/concatSep ", " elements ++ "]"
  
  in  let renderPeople
          : List Person → Text
          =   λ(people : List Person)
            → renderList (List/map Person Text renderPerson people)
  
  in  renderPeople
      [ { name = "John", age = 23 }
      , TODO Person  -- ← You can also leave values unimplemented
      , { name = "Mary", age = 24 }
      ]
```

The type `∀(a : Type) → a` is an impossible type that can never be created in Dhall,
so the `TODO` function argument can never be satisfied.  However, despite that we can
still type-check the function and normalize the function body:

```bash
$ dhall --annotate <<< './example.dhall'
```
```haskell
  (   λ(TODO : ∀(a : Type) → a)
    →     "["
      ++  (     TODO
                ({ age : Natural, name : Text } → Text)
                { age = 23, name = "John" }
            ++  ", "
            ++  TODO
                ({ age : Natural, name : Text } → Text)
                (TODO { age : Natural, name : Text })
            ++  ", "
            ++  TODO
                ({ age : Natural, name : Text } → Text)
                { age = 24, name = "Mary" }
          )
      ++  "]"
  )
: ∀(TODO : ∀(a : Type) → a) → Text
```