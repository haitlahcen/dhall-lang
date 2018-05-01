> Can I create a function with default values for function arguments?

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