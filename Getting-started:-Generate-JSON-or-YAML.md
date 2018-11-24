This tutorial teaches you how to:

* install the `dhall-to-json` and `dhall-to-yaml` executables
* author a Dhall configuration
* generate JSON or YAML from the Dhall configuration

This tutorial does not cover all of the features of the Dhall configuration
language.  The goal of this tutorial is to walk through basic tricks to simplify
repetitive JSON configuration files.

This tutorial assumes that you are comfortable with the command line.  If not,
then read this introduction to the command line:

* [Learn Enough Command Line to Be Dangerous][command-line]

This tutorial also assumes that you understand basic programming concepts like
records and functions.

## Installation

> **NOTE**: This tutorial assumes that you are using version 1.2.5 or later
> of the `dhall-json` package.  Some of the following examples will not
> work correctly for older versions of the package.  For more details, see:
>
> [[Migration: Swapped syntax for Natural numbers and Integers|Migration: Swapped syntax for Natural numbers and Integers]]
>
> [[Migration: Deprecation of constructors keyword|Migration: Deprecation of constructors keyword]]

You will need to install the `dhall-json` package, which provides both the
`dhall-to-json` and `dhall-to-yaml` executables.  The following sections
provide recommended installation instructions for Windows, OS X, and Linux
operating systems:

### Install using Brew (OS X)

Run the following command to install the `dhall-json` package using `brew`:

```bash
$ brew install dhall-json
```

That will install the executables underneath the `/usr/local/bin` directory,
which should already be on your executable search `PATH` for most correctly
configured systems.

Run the following commands to verify that `brew` installed the executables:

```bash
$ /usr/local/bin/dhall-to-json --help
$ /usr/local/bin/dhall-to-yaml --help
```

... and then run the following commands to verify that the `dhall-to-json`
executable is on your executable search `PATH`:

```bash
$ dhall-to-json --help
$ dhall-to-yaml --help
```

### Install a statically linked binary (Linux)

Run the following command to download the `dhall-json` executable:

```bash
$ curl --location http://hydra.dhall-lang.org/job/dhall-haskell/master/tarball-dhall-json/latest/download-by-type/file/binary-dist | tar --extract --bzip2
```

That should create a `./bin` subdirectory underneath your current directory
containing two executables:

```bash
$ ls ./bin
dhall-to-json  dhall-to-yaml
```

Run the following commands to verify that the executables work:

```bash
$ ./bin/dhall-to-json --help
$ ./bin/dhall-to-yaml --help
```

... and then copy those executables to `/usr/local/bin`:

```bash
$ cp ./bin/dhall-to-{json,yaml} /usr/local/bin
```

... and then run the following command to verify that the two
executables are on your executable search `PATH`:

```bash
$ dhall-to-json --help
$ dhall-to-yaml --help
```

### Install using `stack`, the Haskell build tool (Windows)

> **Windows users**: Install [Git for Windows](http://gitforwindows.org/), which
> includes Git Bash: a command line environment complete with Unix utilities.
> Run the following commands within a Git Bash shell.

Install `stack` by following the instructions on <https://haskellstack.org>

Then run the following commands to install the `dhall-json` package:

```bash
$ stack setup
$ stack install dhall-json
```

This will install the `dhall-to-json` and `dhall-to-yaml` executables to
some shared directory (such as `%APPDATA%\local\bin` on Windows or
`~/.local/bin` on Linux / OS X).

`stack` will remind you to add the above shared directory to your executable
search `PATH`.  Don't forget to do this!

Run the following command to verify that `stack` installed the executable:

```bash
$ ~/.local/bin/dhall-to-json --help
$ ~/.local/bin/dhall-to-yaml --help
```

... and then run the following command to verify that the executable is on your
executable search `PATH`:

```bash
$ dhall-to-json --help
$ dhall-to-yaml --help
```

## Smoke test

Now you can test drive generating JSON from a Dhall expression.

The `dhall-to-json` executable takes a Dhall program on standard input and emits
JSON to standard output.

> **Exercise:** Try to guess what JSON the following command will output:
> 
> ```bash
> $ dhall-to-json <<< '{ foo = [1, 2, 3], bar = True }'
> ```
> 
> ... then run the command to test your guess.

> **Note:** `<<<` is a Bash operator that feeds the string on the right as
> standard input to the command on the left.  The above command could have also
> been written as:
> 
> ```bash
> $ echo '{ foo = [1, 2, 3], bar = True }' | dhall-to-json
> ```

> **Exercise:** What Dhall expression generates the following JSON:
>
> ```json
> [{"x":1,"y":"ABC"}]
> ```

The `dhall-to-json` help output indicates that the executable accepts a
`--pretty` flag:

```bash
$ dhall-to-json --help
Usage: dhall-to-json [--explain] [--pretty] [--omitNull] ([--key ARG]
                     [--value ARG] | [--noMaps])
  Compile Dhall to JSON

Available options:
  -h,--help                Show this help text
  --explain                Explain error messages in detail
  --pretty                 Pretty print generated JSON
  --omitNull               Omit record fields that are null
  --key ARG                Reserved key field name for association
                           lists (default: mapKey)
  --value ARG              Reserved value field name for association
                           lists (default: mapValue)
  --noMaps                 Disable conversion of association lists to
                           homogeneous maps
```

> **Exercise:** Run this command to generate pretty-printed JSON output:
> 
> ```bash
> $ dhall-to-json --pretty <<< '{ foo = [1, 2, 3], bar = True }'
> ```

## Imports

At some point our Dhall expressions will no longer fit on the command line.  We
can save large expressions to files and then reference them within other Dhall
expressions.

> **Exercise:** Save the following Dhall expression to a file named
> `example.dhall` in your current working directory:
> 
> ```haskell
> { foo = True
> , bar = [1, 2, 3, 4, 5]
> , baz = "ABC"
> }
> ```
> 
> What do you think the following command will output?
> 
> ```bash
> $ dhall-to-json --pretty <<< '[ ./example.dhall, ./example.dhall ]'
> ```
>
> Test your guess!

## Types

The Dhall configuration language is fairly similar to JSON if you ignore
Dhall's programming language features.  They both have records, lists, numbers,
strings, and boolean values.  However, Dhall is typed and rejects some
configurations that JSON would normally accept.

For example, the following command fails because Dhall requires lists to have
elements of the same type:

```bash
$ dhall-to-json <<< '[ 1, True ]'

Error: List elements should all have the same type

- Natural
+ Bool

True 

(stdin):1:6
```

The error messages are terse by default, but if you check the `--help` output
you can see that the executable accepts an `--explain` flag:

```bash
$ dhall-to-json --help
Usage: dhall-to-json [--explain] [--pretty] [--omitNull] ([--key ARG]
                     [--value ARG] | [--noMaps])
  Compile Dhall to JSON

Available options:
  -h,--help                Show this help text
  --explain                Explain error messages in detail
  --pretty                 Pretty print generated JSON
  --omitNull               Omit record fields that are null
  --key ARG                Reserved key field name for association
                           lists (default: mapKey)
  --value ARG              Reserved value field name for association
                           lists (default: mapValue)
  --noMaps                 Disable conversion of association lists to
                           homogeneous maps
```

> **Exercise**: Add the `--explain` flag to the previous command:
> 
> ```bash
> $ dhall-to-json --explain <<< '[ 1, True ]'
> ```
> 
> ... and read the full explanation for why the executable rejected the Dhall
> expression

Dhall also supports type annotations, which are the Dhall analog of a JSON
schema.  For example:

```bash
$ dhall-to-json <<< '{ foo = 1, bar = True } : { foo : Natural, bar : Bool }'
{"foo":1,"bar":true}
```

Anything in Dhall can be imported from another file, including the type in a
type annotation.  This means that you can save the type annotation to a file:

```bash
$ echo '{ foo : Natural, bar : Bool }' > schema.dhall
```

... and reference that file in a type annotation:

```bash
$ dhall-to-json <<< '{ foo = 1, bar = True } : ./schema.dhall'
{"foo":1,"bar":true}
```

If the expression doesn't match the "schema" (i.e. the type annotation) then
"validation fails" (i.e. you get a type error):

```bash
$ dhall-to-json <<< '{ foo = 1, baz = True } : ./schema.dhall'


Error: Expression doesn't match annotation

{ - bar : …
, + baz : …
, …
}

{ foo = 1, baz = True } : ./schema.dhall


(stdin):1:1
```

> **Exercise:** Add the `--explain` flag to the above command to see why the
> expression failed to validate against the schema.

## Variables

Dhall also differs from JSON by offering some programming language features.
For example, you can reduce repetition by using a `let` expression to define a
variable which can be referenced multiple times.

```bash
$ dhall-to-json <<< 'let x = [1, 2, 3] in [x, x, x]'
```
```json
[[1,2,3],[1,2,3],[1,2,3]]
```

You can define multiple variables using multiple `let`s, like this:

```bash
$ dhall-to-json <<< 'let x = 1 let y = [x, x] in [y, y]' 
```
```json
[[1,1],[1,1]]
```

The Dhall language is whitespace-insensitive (just like JSON), so this program:

```haskell
let x = 1 let y = 2 in [x, y]
```

... is the same as this program:

```haskell
let x = 1
let y = 2
in  [x, y]
```

> **Exercise:** Save the following Dhall configuration to `employees.dhall`:
> 
> ```haskell
> let job = { department = "Data Platform", title = "Software Engineer" }
> 
> let john = { age = 23, name = "John Doe", position = job }
> 
> let alice = { age = 24, name = "Alice Smith", position = job }
> 
> in  [ john, alice ]
> ```
>
> What do you think the following command will output:
>
> ```bash
> $ dhall-to-json --pretty <<< './employees.dhall' 
> ```
>
> Test your guess!

> **Exercise:** This JSON is repetitive
> 
> ```json
> [
>     {
>         "address": {
>             "state": "TX",
>             "street": "Main Street",
>             "city": "Austin",
>             "number": "9999"
>         },
>         "name": "John Doe"
>     },
>     {
>         "address": {
>             "state": "TX",
>             "street": "Main Street",
>             "city": "Austin",
>             "number": "9999"
>         },
>         "name": "Jane Doe"
>     },
>     {
>         "address": {
>             "state": "TX",
>             "street": "Main Street",
>             "city": "Austin",
>             "number": "9999"
>         },
>         "name": "Janet Doe"
>     }
> ]
> ```
>
> Try to use a less repetitive Dhall configuration file to generate the above
> JSON output.

## Functions

Dhall also lets you write anonymous functions of the form:

```haskell
\(inputName : inputType) -> output
```

... which you can also write using Unicode characters if you prefer:

```haskell
λ(inputName : inputType) → output
```

This tutorial will use the Unicode syntax for functions in the following
examples.  If you prefer the Unicode syntax you can learn how to input
Unicode on your computer by following these instructions:

* [Wikipedia - Unicode input](https://en.wikipedia.org/wiki/Unicode_input)

... and using the following Unicode code points for lambdas and arrows:

* `λ` (U+03BB)
* `→` (U+2192)

For example, here is an anonymous function that takes a single argument named
`x` of type `Natural` and returns a list of two `x`s:

```haskell
λ(x : Natural) → [x, x]
```

You can apply an anonymous function directly to an argument like this:

```bash
$ dhall-to-json <<< '(λ(x : Natural) → [x, x]) 2'
```
```json
[2,2]
```

More commonly, you'll use a `let` expression to give the function a name and
then use that name to apply the function to an argument:

```bash
$ dhall-to-json <<< 'let twice = λ(x : Natural) → [x, x] in twice 2'
```
```json
[2,2]
```

> **Exercise**: What JSON do you think this Dhall configuration file will
> generate?
>
> ```haskell
> let smallServer =
>         λ(hostName : Text)
>       → { cpus =
>             1
>         , gigabytesOfRAM =
>             1
>         , hostName =
>             hostName
>         , terabytesOfDisk =
>             1
>         }
> 
> let mediumServer =
>         λ(hostName : Text)
>       → { cpus =
>             8
>         , gigabytesOfRAM =
>             16
>         , hostName =
>             hostName
>         , terabytesOfDisk =
>             4
>         }
> 
> let largeServer =
>         λ(hostName : Text)
>       → { cpus =
>             64
>         , gigabytesOfRAM =
>             256
>         , hostName =
>             hostName
>         , terabytesOfDisk =
>             16
>         }
> 
> in  [ smallServer "eu-west.example.com"
>     , largeServer "us-east.example.com"
>     , largeServer "ap-northeast.example.com"
>     , mediumServer "us-west.example.com"
>     , smallServer "sa-east.example.com"
>     , largeServer "ca-central.example.com"
>     ]
> ```
>
> Test your guess!

You can nest anonymous functions to create a function of multiple arguments:

```bash
$ dhall-to-json <<< 'let both = λ(x : Natural) → λ(y : Natural) → [x, y] in both 1 2'
```
```json
[1,2]
```

> **Exercise**: What JSON do you think this Dhall configuration file will
> generate?
>
> ```haskell
> let educationalBook =
>         λ(publisher : Text)
>       → λ(title : Text)
>       → { category =
>             "Nonfiction"
>         , department =
>             "Books"
>         , publisher =
>             publisher
>         , title =
>             title
>         }
> 
> let makeOreilly = educationalBook "O'Reilly Media"
> 
> in  [ makeOreilly "Microservices for Java Developers"
>     , educationalBook "Addison Wesley" "The Go Programming Language"
>     , makeOreilly "Parallel and Concurrent Programming in Haskell"
>     ]
> ```
> 
> Test your guess!

## Combining records

Dhall provides the `/\` operator for merging two records, which you can also
represent using the Unicode `∧` character (U+2227).

For example:

```bash
$ dhall-to-json <<< '{ foo = 1 } ∧ { bar = 2}'
```
```json
{"foo":1,"bar":2}
```

... is the same as:

```bash
$ dhall-to-json <<< '{ foo = 1, bar = 2}'
```
```json
{"foo":1,"bar":2}
```

We can rewrite our previous server configuration example to use this operator
instead of using functions:

```haskell
let smallServer = { cpus = 1, gigabytesOfRAM = 1, terabytesOfDisk = 1 }

let mediumServer = { cpus = 8, gigabytesOfRAM = 16, terabytesOfDisk = 4 }

let largeServer = { cpus = 64, gigabytesOfRAM = 256, terabytesOfDisk = 16 }

in  [ smallServer ∧ { hostName = "eu-west.example.com" }
    , largeServer ∧ { hostName = "us-east.example.com" }
    , largeServer ∧ { hostName = "ap-northeast.example.com" }
    , mediumServer ∧ { hostName = "us-west.example.com" }
    , smallServer ∧ { hostName = "sa-east.example.com" }
    , largeServer ∧ { hostName = "ca-central.example.com" }
    ]
```

> **Exercise:** Refactor the prevous "educational books" example to also use the
> record merge operator instead of functions

## Operators

You can concatenate two strings using the `++` operator:

```bash
$ dhall-to-json <<< '[ "ABC" ++ "DEF" ]'
```
```json
["ABCDEF"]
```

... and you can concatenate two lists using the `#` operator:

```bash
$ dhall-to-json <<< '[1, 2, 3] # [4, 5, 6]'
```
```json
[1,2,3,4,5,6]
```

> **Exercise:** What JSON do you think the following Dhall expression will
> generate?
>
> ```haskell
> let three = λ(x : Text) → [x ++ x ++ x] in three "A" # three "B" # three "C"
> ```
>
> Test your guess!

> **Exercise:** Write a non-repetitive Dhall expression that generates the
> following JSON:
>
> ```json
> {
>     "administrativeUsers": [
>         "alice",
>         "bob",
>         "carol"
>     ],
>     "ordinaryUsers": [
>         "alice",
>         "bob",
>         "carol",
>         "david",
>         "eve",
>         "frank"
>     ]
> }
> ```

## `Optional` values

Dhall's type system will reject the following common JSON idiom:

```bash
$ dhall-to-json <<< '[ { x = 1 }, { x = 2, y = 3 } ]'


Error: List elements should all have the same type

{ + y : …
, …
}

{ x = 2, y = 3 } 

(stdin):1:14
```

JSON configurations often have lists of records, where different records will
have different sets of fields defined.  Dhall rejects this because adding or
removing a field from a record changes the record's type.

Despite this restriction, we still have a few options for generating the above
JSON.  For example, you can make the `y` field `Optional` (i.e. the Dhall
equivalent of a nullable value), like this:

```haskell
-- optional.dhall

[ { x = 1, y = [] : Optional Natural }
, { x = 2, y = [ 3 ] : Optional Natural }
]
```

`Optional` values look just like lists except that they have at most 1 element
and they have a mandatory type annotation.

`dhall-to-json` by default converts an empty `Optional` value to `null`:

```bash
$ dhall-to-json <<< './optional.dhall'
```
```json
[{"x":1,"y":null},{"x":2,"y":3}]
```

... but also provides a `--omitNull` flag that you can use to omit `null` fields
from records, like this:

```bash
$ dhall-to-json --omitNull <<< './optional.dhall'
```
```json
[{"x":1},{"x":2,"y":3}]
```

## Unions

Sometimes JSON values might differ by more than just the presence or absence of
a record field.  For example, this is valid JSON, too:

```json
[1,true]
```

We would get a type error if we were to naively translate the above JSON to Dhall:

```bash
$ dhall-to-json <<< '[ 1, True ]'


Error: List elements should all have the same type

- Natural
+ Bool

True 

(stdin):1:6
```

However, we can still generate such JSON if we take advantage of Dhall's support
for "unions".  You can think of a "union" as a value that can be one or more
possible types.

For example, the equivalent Dhall configuration would be:

```haskell
-- union.dhall

let Element = < Left : Natural | Right : Bool >

in  [ Element.Left 1, Element.Right True ]
```

Every union type has multiple possible alternatives, each labeled by a name and a
type.  For example, the union type named `Element` in the above Dhall
configuration has two alternatives:

* The first alternative is named `Left` and can store `Natural` numbers
* The second alternative is named `Right` and can store `Bool`s

The `dhall-to-json` executable strips the names when translating union literals
to JSON.  This trick lets you bridge between strongly typed Dhall configuration
files and their weakly typed JSON equivalents:

```bash
$ dhall-to-json <<< './union.dhall' 
```
```json
[1,true]
```

Here is a more sophisticated example showcasing how each union alternative
can be a record with different fields present:

```haskell
-- package.dhall

let Package =
      < Local :
          { relativePath : Text }
      | GitHub :
          { repository : Text, revision : Text }
      | Hackage :
          { package : Text, version : Text }
      >

in  [ Package.GitHub
      { repository =
          "https://github.com/Gabriel439/Haskell-Turtle-Library.git"
      , revision =
          "ae5edf227b515b34c1cb6c89d9c58ea0eece12d5"
      }
    , Package.Local { relativePath = "~/proj/optparse-applicative" }
    , Package.Local { relativePath = "~/proj/discrimination" }
    , Package.Hackage { package = "lens", version = "4.15.4" }
    , Package.GitHub
      { repository =
          "https://github.com/haskell/text.git"
      , revision =
          "ccbfabedea1cf5b38ff19f37549feaf01225e537"
      }
    , Package.Local { relativePath = "~/proj/servant-swagger" }
    , Package.Hackage { package = "aeson", version = "1.2.3.0" }
    ]
```

... which generates the following JSON:

```json
[
    {
        "repository": "https://github.com/Gabriel439/Haskell-Turtle-Library.git",
        "revision": "ae5edf227b515b34c1cb6c89d9c58ea0eece12d5"
    },
    {
        "relativePath": "~/proj/optparse-applicative"
    },
    {
        "relativePath": "~/proj/discrimination"
    },
    {
        "version": "4.15.4",
        "package": "lens"
    },
    {
        "repository": "https://github.com/haskell/text.git",
        "revision": "ccbfabedea1cf5b38ff19f37549feaf01225e537"
    },
    {
        "relativePath": "~/proj/servant-swagger"
    },
    {
        "version": "1.2.3.0",
        "package": "aeson"
    }
]
```

## Dynamic records

Some programs expect JSON records with a dynamically computed set of fields.  For
example, you might require a list of students to be represented by the following
sample JSON:

```json
{
    "aiden": {
        "age": 16
    },
    "daniel": {
        "age": 17
    },
    "rebecca": {
        "age": 17
    }
}
```

You can't use a Dhall record to store the above students because then the type of
the record would change every time you add or remove a student.

The idiomatic way to encode the above information in Dhall is to use an "association
list" (i.e. a list of key-value pairs), like this:

```haskell
-- students.dhall

[ { mapKey = "daniel", mapValue = { age = 17 } }
, { mapKey = "rebecca", mapValue = { age = 17 } }
, { mapKey = "aiden", mapValue = { age = 16 } }
]
```

... and the `dhall-to-json` executable automatically detects any association list
that uses the field names `mapKey` and `mapValue` and converts that to the
equivalent dynamic JSON record:

```bash
$ dhall-to-json --pretty <<< './students.dhall'
```
```json
{
    "aiden": {
        "age": 16
    },
    "daniel": {
        "age": 17
    },
    "rebecca": {
        "age": 17
    }
}
```

This ensures that  the schema of your Dhall configuration stays the same
but you can still generate JSON records with a dynamically computed set of
fields.

You have the option to disable this feature if you want using the `--noMaps`
flag:

```bash
$ dhall-to-json --pretty --noMaps <<< './students.dhall'
```
```json
[
    {
        "mapKey": "daniel",
        "mapValue": {
            "age": 17
        }
    },
    {
        "mapKey": "rebecca",
        "mapValue": {
            "age": 17
        }
    },
    {
        "mapKey": "aiden",
        "mapValue": {
            "age": 16
        }
    }
]
```

... or you can specify a different set of field names to reserve using the
`--key` and `--value` options if you don't want to reserve the names
`mapKey` and `mapValue`.

## YAML

You can translate all of the above examples to YAML instead of JSON using the
`dhall-to-yaml` executable.  For example:

```bash
$ dhall-to-yaml <<< 'let x = 1 in let y = [x, x] in [y, y]'
```
```yaml
- - 1
  - 1
- - 1
  - 1
```

> **Exercise:** Translate one of the larger previous examples to YAML.

[command-line]: https://www.learnenough.com/command-line-tutorial
[wsl]: https://msdn.microsoft.com/en-us/commandline/wsl/install-win10
[nix]: https://nixos.org/nix/download.html
[high-sierra-bug]: https://github.com/NixOS/nix/issues/1583

## Conclusion

This concludes the tutorial on how to use the Dhall configuration language to
simplify repetitive JSON and YAML configurations.  By this point you should
understand how to some basic features of Dhall and you can learn more by reading
the main language tutorial.
