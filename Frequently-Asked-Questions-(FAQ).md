## Imports relative to my top-level file are not working

All top-level imports are relative to the current working directory.  For example, if you
have a file located at `./foo/bar.dhall` that tries to import `./foo/baz.dhall` via a
relative import:

```bash
$ cat ./foo/bar.dhall
```
```haskell
./baz.dhall
```
```bash
$ cat ./foo/baz.dhall
```
```haskell
1
```

... that relative import will not work correctly if you feed that file to a Dhall
interpreter via standard input:

```bash
$ dhall < ./foo/bar.dhall
↳ ./baz.dhall

Error: Missing file ./baz.dhall
```

This is because the interpreter does not know that the string fed in via standard
input originally came from `./foo/bar.dhall`.  Therefore, the interpreter cannot
process the relative import correctly.

However, the relative import does work correctly if you feed a Dhall program
importing that file to standard input, like this:

```bash
$ echo './foo/bar.dhall' | dhall
```
```haskell
Natural

1
```

In Bash, you can shorten this to:

```bash
$ dhall <<< './foo/bar.dhall'
```
```haskell
Natural

1
```

## Why can't I use a type alias on an empty List?

In practice, this would not work:

```haskell
   let genericRecord = List { mapKey : Text, mapValue : Text }
in ([] : genericRecord)
```

Instead, you have to do this:
```haskell
   let genericRecord = { mapKey : Text, mapValue : Text }
in ([] : List genericRecord)
```

The reason for this is that the type annotation for empty lists is not a real type annotation. It's actually part of the grammar:

```haskell
Syntax
↓↓↓↓↓↓↓↓↓
[] : List ElementType
```

## Can I create a function with default values for function arguments?

The Dhall configuration language does not provide language support for functions with default-valued arguments.  However, you can create records of default values that you can selectively override with new values using the `//` operator.

For example, in Python you can write:

```python
def greet(greeting="Hello", name="John"):
    print("{0}, {1}!".format(greeting, name))

greet()
greet(greeting="Hola")
greet(name="Jane")
greet(greeting="Hola",name="Jane")
```

... which produces this result:

```bash
$ python greet.py
Hello, John!
Hola, John!
Hello, Jane!
Hola, Jane!
```

The Dhall equivalent of the above code would be:

```haskell
    let greet =
            λ(args : { greeting : Text, name : Text })
          → "${args.greeting}, ${args.name}!"

in  let default = { greeting = "Hello", name = "John" }

in  ''
    ${greet default}
    ${greet (default ⫽ { greeting = "Hola" })}
    ${greet (default ⫽ { name = "Jane" })}
    ${greet { greeting = "Hola", name = "Jane" }}
    ''
```

... which produces the same result:

```bash
$ dhall-to-text < test.dhall
Hello, John!
Hola, John!
Hello, Jane!
Hola, Jane!
```