This is a passive tutorial designed for you to read along.  If
you would like a longer tutorial where you can install the program
and get your hands dirty following along with examples then read:

* [[Getting started: Generate JSON or YAML|Getting started: Generate JSON or YAML]]

Let's motivate the Dhall configuration language by considering the
following JSON configuration representing Haskell package metadata
(wrapped to 80 columns):

```json
[
    {
        "name": "dhall",
        "author": "Gabriel Gonzalez",
        "license": "Copyright 2017 Gabriel Gonzalez\n\nRedistribution and use in
 source and binary forms, with or without\nmodification, are permitted provided
that the following conditions are met:\n\n1. Redistributions of source code must
 retain the above copyright notice, this\n   list of conditions and the followin
g disclaimer.\n\n2. Redistributions in binary form must reproduce the above copy
right notice,\n   this list of conditions and the following disclaimer in the do
cumentation\n   and/or other materials provided with the distribution.\n\n3. Nei
ther the name of the copyright holder nor the names of its contributors\n   may
be used to endorse or promote products derived from this software without\n   sp
ecific prior written permission.\n\nTHIS SOFTWARE IS PROVIDED BY THE COPYRIGHT H
OLDERS AND CONTRIBUTORS \"AS IS\" AND\nANY EXPRESS OR IMPLIED WARRANTIES, INCLUD
ING, BUT NOT LIMITED TO, THE IMPLIED\nWARRANTIES OF MERCHANTABILITY AND FITNESS
FOR A PARTICULAR PURPOSE ARE\nDISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER
 OR CONTRIBUTORS BE LIABLE\nFOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMP
LARY, OR CONSEQUENTIAL\nDAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF S
UBSTITUTE GOODS OR\nSERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRU
PTION) HOWEVER\nCAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRI
CT LIABILITY,\nOR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OU
T OF THE USE\nOF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAG
E.\n"
    },
    {
        "name": "conduit",
        "author": "Michael Snoyman",
        "license": "Copyright 2012 Michael Snoyman\n\nPermission is hereby grant
ed, free of charge, to any person obtaining a copy of this software and associat
ed documentation files (the \"Software\"), to deal in the Software without restr
iction, including without limitation the rights to use, copy, modify, merge, pub
lish, distribute, sublicense, and/or sell copies of the Software, and to permit
persons to whom the Software is furnished to do so, subject to the following con
ditions:\n\nThe above copyright notice and this permission notice shall be inclu
ded in all copies or substantial portions of the Software.\n\nTHE SOFTWARE IS PR
OVIDED \"AS IS\", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BU
T NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PUR
POSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRA
CT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE O
R THE USE OR OTHER DEALINGS IN THE SOFTWARE.\n"
    },
    {
        "name": "async",
        "author": "Simon Marlow",
        "license": "Copyright 2012 Simon Marlow\n\nRedistribution and use in sou
rce and binary forms, with or without\nmodification, are permitted provided that
 the following conditions are met:\n\n1. Redistributions of source code must ret
ain the above copyright notice, this\n   list of conditions and the following di
sclaimer.\n\n2. Redistributions in binary form must reproduce the above copyrigh
t notice,\n   this list of conditions and the following disclaimer in the docume
ntation\n   and/or other materials provided with the distribution.\n\n3. Neither
 the name of the copyright holder nor the names of its contributors\n   may be u
sed to endorse or promote products derived from this software without\n   specif
ic prior written permission.\n\nTHIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDE
RS AND CONTRIBUTORS \"AS IS\" AND\nANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING,
 BUT NOT LIMITED TO, THE IMPLIED\nWARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE ARE\nDISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR
CONTRIBUTORS BE LIABLE\nFOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY
, OR CONSEQUENTIAL\nDAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBST
ITUTE GOODS OR\nSERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTIO
N) HOWEVER\nCAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT L
IABILITY,\nOR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF
 THE USE\nOF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.\n
"
    },
    {
        "name": "system-filepath",
        "author": "John Milikin",
        "license": "Copyright 2010 John Milikin\n\nPermission is hereby granted,
 free of charge, to any person obtaining a copy of this software and associated
documentation files (the \"Software\"), to deal in the Software without restrict
ion, including without limitation the rights to use, copy, modify, merge, publis
h, distribute, sublicense, and/or sell copies of the Software, and to permit per
sons to whom the Software is furnished to do so, subject to the following condit
ions:\n\nThe above copyright notice and this permission notice shall be included
 in all copies or substantial portions of the Software.\n\nTHE SOFTWARE IS PROVI
DED \"AS IS\", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT N
OT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOS
E AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIA
BLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
 TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR T
HE USE OR OTHER DEALINGS IN THE SOFTWARE.\n"
    }
]
```

---

**User:** *"How can I make the above configuration file easier to read and
edit?"*

The above configuration file is difficult to read because of the large
license texts formatted as long uninterrupted lines.  We can make the
configuration more readable by converting the JSON configuration file to a Dhall
configuration file, which supports multi-line string literals (like YAML):

```haskell
-- example0.dhall

[   {   name    = "dhall"
    ,   author  = "Gabriel Gonzalez"
    ,   license = ''
            Copyright 2017 Gabriel Gonzalez

            Redistribution and use in source and binary forms, with or without
            modification, are permitted provided that the following conditions are met:

            1. Redistributions of source code must retain the above copyright notice, this
               list of conditions and the following disclaimer.

            2. Redistributions in binary form must reproduce the above copyright notice,
               this list of conditions and the following disclaimer in the documentation
               and/or other materials provided with the distribution.

            3. Neither the name of the copyright holder nor the names of its contributors
               may be used to endorse or promote products derived from this software without
               specific prior written permission.

            THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
            ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
            WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
            DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
            FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
            DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
            SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
            CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
            OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
            OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
        ''
    }
,   {   name    = "conduit"
    ,   author  = "Michael Snoyman"
    ,   license = ''
            Copyright 2012 Michael Snoyman

            Permission is hereby granted, free of charge, to any person obtaining a copy of
            this software and associated documentation files (the "Software"), to deal in
            the Software without restriction, including without limitation the rights to
            use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
            of the Software, and to permit persons to whom the Software is furnished to do
            so, subject to the following conditions:

            The above copyright notice and this permission notice shall be included in all
            copies or substantial portions of the Software.

            THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
            IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
            FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
            AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
            LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
            OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
            SOFTWARE.
        ''
    }
,   {   name    = "async"
    ,   author  = "Simon Marlow"
    ,   license = ''
            Copyright 2012 Simon Marlow

            Redistribution and use in source and binary forms, with or without
            modification, are permitted provided that the following conditions are met:

            1. Redistributions of source code must retain the above copyright notice, this
               list of conditions and the following disclaimer.

            2. Redistributions in binary form must reproduce the above copyright notice,
               this list of conditions and the following disclaimer in the documentation
               and/or other materials provided with the distribution.

            3. Neither the name of the copyright holder nor the names of its contributors
               may be used to endorse or promote products derived from this software without
               specific prior written permission.

            THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
            ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
            WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
            DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
            FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
            DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
            SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
            CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
            OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
            OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
        ''
    }
,   {   name    = "system-filepath"
    ,   author  = "John Milikin"
    ,   license = ''
            Copyright 2010 John Milikin

            Permission is hereby granted, free of charge, to any person obtaining a copy of
            this software and associated documentation files (the "Software"), to deal in
            the Software without restriction, including without limitation the rights to
            use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
            of the Software, and to permit persons to whom the Software is furnished to do
            so, subject to the following conditions:

            The above copyright notice and this permission notice shall be included in all
            copies or substantial portions of the Software.

            THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
            IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
            FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
            AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
            LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
            OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
            SOFTWARE.
        ''
    }
]
```

... which we can always convert back to the original JSON configuration by
running:

```
$ dhall-to-json --pretty <<< 'example0.dhall'
[
    {
        "name": "dhall",
        "author": "Gabriel Gonzalez",
        "license": "Copyright 2017 Gabriel Gonzalez\n\nRedistribution and us..."
    },
    {
        "name": "conduit",
        "author": "Michael Snoyman",
        "license": "Copyright 2012 Michael Snoyman\n\nPermission is hereby g..."
    },
    {
        "name": "async",
        "author": "Simon Marlow",
        "license": "Copyright 2012 Simon Marlow\n\nRedistribution and use in..."
    },
    {
        "name": "system-filepath",
        "author": "John Milikin",
        "license": "Copyright 2010 John Milikin\n\nPermission is hereby gran..."
    }
]
```

---

**User:** *"I still can't tell at a glance which license is which"*

We could add comments with the name of each license (since Dhall, unlike JSON,
supports comments):

```haskell
[   {   name    = "dhall"
    ,   author  = "Gabriel Gonzalez"

        -- BSD 3-Clause
    ,   license = ''
            Copyright 2017 Gabriel Gonzalez

            Redistribution and use in source and binary forms, with or without
            ...
            OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
        ''
    }
,   {   name    = "conduit"
    ,   author  = "Michael Snoyman"

        -- MIT
    ,   license = ''
            Copyright 2012 Michael Snoyman

            Permission is hereby granted, free of charge, to any person obtaining a copy of
            ...
            SOFTWARE.
        ''
    }
,   {   name    = "async"
    ,   author  = "Simon Marlow"

        -- BSD 3-Clause
    ,   license = ''
            Copyright 2012 Simon Marlow

            Redistribution and use in source and binary forms, with or without
            ...
            OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
        ''
    }
,   {   name    = "system-filepath"
    ,   author  = "John Milikin"

        -- MIT
    ,   license = ''
            Copyright 2010 John Milikin

            Permission is hereby granted, free of charge, to any person obtaining a copy of
            ...
            SOFTWARE.
        ''
    }
]
```

... or we could define named functions to build each type of license, like this:

```haskell
-- example1.dhall

    let BSD-3-Clause = λ(args : { year : Natural, author : Text }) → ''
            Copyright ${Natural/show args.year} ${args.author}

            Redistribution and use in source and binary forms, with or without
            modification, are permitted provided that the following conditions are met:

            1. Redistributions of source code must retain the above copyright notice, this
               list of conditions and the following disclaimer.

            2. Redistributions in binary form must reproduce the above copyright notice,
               this list of conditions and the following disclaimer in the documentation
               and/or other materials provided with the distribution.

            3. Neither the name of the copyright holder nor the names of its contributors
               may be used to endorse or promote products derived from this software without
               specific prior written permission.

            THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
            ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
            WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
            DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
            FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
            DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
            SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
            CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
            OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
            OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
        ''

in  let MIT = λ(args : { year : Natural, author : Text }) → ''
            Copyright ${Natural/show args.year} ${args.author}

            Permission is hereby granted, free of charge, to any person obtaining a copy of
            this software and associated documentation files (the "Software"), to deal in
            the Software without restriction, including without limitation the rights to
            use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
            of the Software, and to permit persons to whom the Software is furnished to do
            so, subject to the following conditions:

            The above copyright notice and this permission notice shall be included in all
            copies or substantial portions of the Software.

            THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
            IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
            FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
            AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
            LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
            OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
            SOFTWARE.
        ''

in  [   {   name    = "dhall"
        ,   author  = "Gabriel Gonzalez"
        ,   license = BSD-3-Clause { year = 2017, author = "Gabriel Gonzalez" }
        }
    ,   {   name    = "conduit"
        ,   author  = "Michael Snoyman"
        ,   license = MIT { year = 2012, author = "Michael Snoyman" }
        }
    ,   {   name    = "async"
        ,   author  = "Simon Marlow"
        ,   license = BSD-3-Clause { year = 2012, author = "Simon Marlow" }
        }
    ,   {   name    = "system-filepath"
        ,   author  = "John Milikin"
        ,   license = MIT { year = 2010, author = "John Milikin" }
        }
    ]
```

Now we can easily tell at a glance which license each package uses without the
use of comments

Dhall supports anonymous functions using the following syntax:

```haskell
λ(inputName : inputType) → output
```

For example:

```
  The name of the function input is "args", short for "arguments"
  ↓
λ(args : { year : Natural, author : Text }) → ...
         ↑
         "args" is a record with two fields named "year" and "author"
```

---

**User:** *"I can't type Unicode characters like `'λ'` and `'→'`"*

Dhall supports ASCII equivalents of all Unicode characters, such as `\` instead
of `λ` and `->` instead of `→`.  However, this tutorial will use Unicode
everywhere because it's prettier.

If you would like to try to type Unicode characters, you can follow these
instructions for various platforms:

* [Unicode input](https://en.wikipedia.org/wiki/Unicode_input)

The Unicode code points for the above symbols are:

* `λ (U+03BB)`
* `→ (U+2192)`

---

**User:** *"The license text is still too distracting.  Could we move it out of
this configuration into another file?"*

We can move anything in Dhall (values, functions, types) into separate files and
they can refer to each other by their relative or absolute paths.  For example,
we can factor out the `MIT` and `BSD-3-Clause` functions out into separate
files, like this:

```haskell
-- BSD-3-Clause.dhall

λ(args : { year : Natural, author : Text }) → ''
    Copyright ${Natural/show args.year} ${args.author}

    Redistribution and use in source and binary forms, with or without
    modification, are permitted provided that the following conditions are met:

    1. Redistributions of source code must retain the above copyright notice, this
       list of conditions and the following disclaimer.

    2. Redistributions in binary form must reproduce the above copyright notice,
       this list of conditions and the following disclaimer in the documentation
       and/or other materials provided with the distribution.

    3. Neither the name of the copyright holder nor the names of its contributors
       may be used to endorse or promote products derived from this software without
       specific prior written permission.

    THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
    ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
    WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
    DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
    FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
    DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
    SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
    CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
    OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
    OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
''
```

```haskell
-- MIT.dhall

λ(args : { year : Natural, author : Text }) → ''
    Copyright ${Natural/show args.year} ${args.author}

    Permission is hereby granted, free of charge, to any person obtaining a copy of
    this software and associated documentation files (the "Software"), to deal in
    the Software without restriction, including without limitation the rights to
    use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
    of the Software, and to permit persons to whom the Software is furnished to do
    so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.
''
```

... and then refer to these files within our original configuration:

```haskell
-- example2.dhall

[   {   name    = "dhall"
    ,   author  = "Gabriel Gonzalez"
    ,   license = ./BSD-3-Clause.dhall { year = 2017, author = "Gabriel Gonzalez" }
    }
,   {   name    = "conduit"
    ,   author  = "Michael Snoyman"
    ,   license = ./MIT.dhall { year = 2012, author = "Michael Snoyman" }
    }
,   {   name    = "async"
    ,   author  = "Simon Marlow"
    ,   license = ./BSD-3-Clause.dhall { year = 2012, author = "Simon Marlow" }
    }
,   {   name    = "system-filepath"
    ,   author  = "John Milikin"
    ,   license = ./MIT.dhall { year = 2010, author = "John Milikin" }
    }
]
```

Files containing Dhall expressions don't have to end with a `.dhall` file
extension.  This is purely a convention

---

**User:** *"There's still some duplication: there is an unspoken invariant that
the package author has to match the license author"*

We can automate that away, too:

```haskell
-- example3.dhall

    let makePackage =
        λ(args : {   name        : Text
                 ,   author      : Text
                 ,   year        : Natural
                 ,   makeLicense : { year : Natural, author : Text } → Text
                 }
        )
    →   {   name    = args.name
        ,   author  = args.author
        ,   license = args.makeLicense { year = args.year, author = args.author }
        }

in  [   makePackage {   name        = "dhall"
                    ,   author      = "Gabriel Gonzalez"
                    ,   year        = 2017
                    ,   makeLicense = ./BSD-3-Clause.dhall
                    }
    ,   makePackage {   name        = "conduit"
                    ,   author      = "Michael Snoyman"
                    ,   year        = 2012
                    ,   makeLicense = ./MIT.dhall
                    }
    ,   makePackage {   name        = "async"
                    ,   author      = "Simon Marlow"
                    ,   year        = 2012
                    ,   makeLicense = ./BSD-3-Clause.dhall
                    }
    ,   makePackage {   name        = "system-filepath"
                    ,   author      = "John Milikin"
                    ,   year        = 2010
                    ,   makeLicense = ./MIT.dhall
                    }
    ]
```

---

**User:** *"Still too much duplication.  I plan on adding a lot more entries and
I don't want to type `makePackage` for every entry in the list"*

Dhall provides a Prelude of utilities to automate common tasks.  For example,
the Prelude provides a `map` function that transforms every element of the list
with the same function (such as `makePackage`):

```haskell
-- example4.dhall

    let map = https://raw.githubusercontent.com/dhall-lang/Prelude/35deff0d41f2bf86c42089c6ca16665537f54d75/List/map

in  let makePackage =
        λ(args : {   name        : Text
                 ,   author      : Text
                 ,   year        : Natural
                 ,   makeLicense : { year : Natural, author : Text } → Text
                 }
        )
    →   {   name    = args.name
        ,   author  = args.author
        ,   license = args.makeLicense { year = args.year, author = args.author }
        }

in  map

    {   name        : Text
    ,   author      : Text
    ,   year        : Natural
    ,   makeLicense : { year : Natural, author : Text } → Text
    }

    {   name    : Text
    ,   author  : Text
    ,   license : Text
    }

    makePackage

    [   {   name        = "dhall"
        ,   author      = "Gabriel Gonzalez"
        ,   year        = 2017
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "conduit"
        ,   author      = "Michael Snoyman"
        ,   year        = 2012
        ,   makeLicense = ./MIT.dhall
        }
    ,   {   name        = "async"
        ,   author      = "Simon Marlow"
        ,   year        = 2012
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "system-filepath"
        ,   author      = "John Milikin"
        ,   year        = 2010
        ,   makeLicense = ./MIT.dhall
        }
    ]
```

You can import functions, values, and types from URLs the same way that you
import them from paths and this is how Dhall distributes the Prelude

Every function from the Prelude has documentation, examples, and a type
signature:

```bash
$ curl https://raw.githubusercontent.com/dhall-lang/Prelude/35deff0d41f2bf86c42089c6ca16665537f54d75/List/map
{-
Tranform a list by applying a function to each element

Examples:

./map Natural Bool Natural/even ([2, 3, 5] : List Natural)
= [True, False, False] : List Bool

./map Natural Bool Natural/even ([] : List Natural)
= [] : List Bool
-}
let map : ∀(a : Type) → ∀(b : Type) → (a → b) → List a → List b
    =   λ(a : Type)
    →   λ(b : Type)
    →   λ(f : a → b)
    →   λ(xs : List a)
    →   List/build
        b
        (   λ(list : Type)
        →   λ(cons : b → list → list)
        →   List/fold a xs list (λ(x : a) → cons (f x))
        )

in  map
```

The type signature for `map`:

```haskell
let map : ∀(a : Type) → ∀(b : Type) → (a → b) → List a → List b
```

... says that `map` takes four arguments:

* The first argument is the element type of the input list
* The second argument is the element type of the output list
* The third argument is the function to apply to each element of the list
* The fourth argument is the input list

... and the result is the output list

You can browse the latest version of the Prelude online by visiting:

* [http://prelude.dhall-lang.org/](http://prelude.dhall-lang.org/)

You can also import functions from the latest version of the Prelude using the
same domain:

```haskell
    let map = http://prelude.dhall-lang.org/List/map

in  ...
```

... although be cautious when doing so since this URL is mutable and you can't
guarantee that you get the same result every time.

---

**User:** *"These types are still long and repetitive.  One type shows up twice
in the source code"*

You can use `let` expressions to define "type synonyms":

```haskell
-- example5.dhall

    let map = https://raw.githubusercontent.com/dhall-lang/Prelude/35deff0d41f2bf86c42089c6ca16665537f54d75/List/map

in  let Input =
          {   name        : Text
          ,   author      : Text
          ,   year        : Natural
          ,   makeLicense : { year : Natural, author : Text } → Text
          }

in  let Output =
          {   name    : Text
          ,   author  : Text
          ,   license : Text
          }

in  let makePackage =
        λ(args : Input)
    →   {   name    = args.name
        ,   author  = args.author
        ,   license = args.makeLicense { year = args.year, author = args.author }
        }

in  map Input Output makePackage

    [   {   name        = "dhall"
        ,   author      = "Gabriel Gonzalez"
        ,   year        = 2017
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "conduit"
        ,   author      = "Michael Snoyman"
        ,   year        = 2012
        ,   makeLicense = ./MIT.dhall
        }
    ,   {   name        = "async"
        ,   author      = "Simon Marlow"
        ,   year        = 2012
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "system-filepath"
        ,   author      = "John Milikin"
        ,   year        = 2010
        ,   makeLicense = ./MIT.dhall
        }
    ]
```

... or you can store the types in files:

```haskell
-- Input.dhall
{   name        : Text
,   author      : Text
,   year        : Natural
,   makeLicense : { year : Natural, author : Text } → Text
}
```

```haskell
-- Output.dhall
{   name    : Text
,   author  : Text
,   license : Text
}
```

... and import those types like any other Dhall expression:

```haskell
-- example5.dhall

    let map = https://raw.githubusercontent.com/dhall-lang/Prelude/35deff0d41f2bf86c42089c6ca16665537f54d75/List/map

in  let makePackage =
        λ(args : ./Input.dhall)
    →   {   name    = args.name
        ,   author  = args.author
        ,   license = args.makeLicense { year = args.year, author = args.author }
        }

in  map ./Input.dhall ./Output.dhall makePackage

    [   {   name        = "dhall"
        ,   author      = "Gabriel Gonzalez"
        ,   year        = 2017
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "conduit"
        ,   author      = "Michael Snoyman"
        ,   year        = 2012
        ,   makeLicense = ./MIT.dhall
        }
    ,   {   name        = "async"
        ,   author      = "Simon Marlow"
        ,   year        = 2012
        ,   makeLicense = ./BSD-3-Clause.dhall
        }
    ,   {   name        = "system-filepath"
        ,   author      = "John Milikin"
        ,   year        = 2010
        ,   makeLicense = ./MIT.dhall
        }
    ]
```

... whichever you prefer.

---

**User:** *"Why doesn't Dhall use JSON-like syntax?"*

JSON isn't the only file format that Dhall supports.  For example, we can
convert our Dhall configuration to YAML:

```bash
$ dhall-to-yaml <<< '/tmp/test/example3'
- name: dhall
  author: Gabriel Gonzalez
  license: ! '
    Copyright 2017 Gabriel Gonzalez


    Redistribution and use in source and binary forms, with or without modification,
    ...
    OF THE POSSIBILITY OF SUCH DAMAGE.

'
- name: conduit
  author: Michael Snoyman
  license: ! '
    Copyright 2012 Michael Snoyman

    Permission is hereby granted, free of charge, to any person obtaining a copy of
    ...
    SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

'
- name: async
  author: Simon Marlow
  license: ! '
    Copyright 2012 Simon Marlow

    Redistribution and use in source and binary forms, with or without modification,
    ...
    OF THE POSSIBILITY OF SUCH DAMAGE.

'
- name: system-filepath
  author: John Milikin
  license: ! '

    Copyright 2010 John Milikin


    Permission is hereby granted, free of charge, to any person obtaining a copy of
    ...
    SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

'
```

... or read our Dhall configuration directly into some programming languages
(like Haskell) without going through a JSON intermediate:

```haskell
{-# LANGUAGE DeriveAnyClass    #-}
{-# LANGUAGE DeriveGeneric     #-}
{-# LANGUAGE OverloadedStrings #-}

import Dhall

data Package = Package
    { name    :: Text
    , author  :: Text
    , license :: Text
    } deriving (Generic, Interpret, Show)

main :: IO ()
main = do
    packages <- input auto "./example.dhall"
    print (packages :: Vector Package)
```

---

**User:** *"Why not just always go through a JSON intermediate?  Every language
already supports JSON"*

You can load more exotic things into the language (like functions) if the
language integrates directly with Dhall:

```haskell
{-# LANGUAGE OverloadedStrings #-}

import Dhall

main :: IO ()
main = do
    twice <- input auto "λ(x : Integer) → [x, x]" :: IO (Integer -> Vector Integer)
    print (twice 5) -- prints: "[5,5]"
```

... and other features, too, like unions

---

**User:** *"What's a union?"*

At this point if you are still curious you should read the longer
[[JSON tutorial|Getting started: Generate JSON or YAML]]

[dhall-haskell]: https://github.com/dhall-lang/dhall-haskell