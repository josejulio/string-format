# String::format

String::format is a small JavaScript utility which adds a `format` method
to strings. It's inspired by and modelled on Python's [`str.format()`][1].

When `format` is invoked on a string, placeholders within the string are
replaced with values determined by the arguments provided. A placeholder
is a sequence of characters beginning with `{` and ending with `}`.

### string.format(value1, value2, ..., valueN)

Placeholders may contain numbers which refer to positional arguments:

```coffeescript
"{0}, you have {1} unread message{2}".format("Holly", 2, "s")
# "Holly, you have 2 unread messages"
```

Unmatched placeholders produce no output:

```coffeescript
"{0}, you have {1} unread message{2}".format("Steve", 1)
# "Steve, you have 1 unread message"
```

A format string may reference a positional argument multiple times:

```coffeescript
"{0} x {0} x {0} = {1}".format(3, 3*3*3)
# "3 x 3 x 3 = 27"
```

Positional arguments may be referenced implicitly:

```coffeescript
"{}, you have {} unread message{}".format("Steve", 1)
# "Steve, you have 1 unread message"
```

A format string must not contain both implicit and explicit references:

```coffeescript
"My name is {} {}. Do you like the name {0}?".format("Lemony", "Snicket")
# ValueError: cannot switch from implicit to explicit numbering
```

`{{` and `}}` in format strings produce `{` and `}`:

```coffeescript
"{{}} creates an empty {} in {}".format("dictionary", "Python")
# "{} creates an empty dictionary in Python"
```

Dot notation may be used to reference object properties:

```coffeescript
bobby = first_name: "Bobby", last_name: "Fischer"
garry = first_name: "Garry", last_name: "Kasparov"

"{0.first_name} {0.last_name} vs. {1.first_name} {1.last_name}".format(bobby, garry)
# "Bobby Fischer vs. Garry Kasparov"
```

When referencing the first positional argument, `0.` may be omitted:

```coffeescript
repo = owner: "pypy", slug: "pypy", followers: [...]

"{owner}/{slug} has {followers.length} followers".format(repo)
# "pypy/pypy has 516 followers"
```

If the referenced property is a method, it is invoked and the result is used
as the replacement string:

```coffeescript
me = name: "David", dob: new Date "26 Apr 1984"

"{name} was born in {dob.getFullYear}".format(me)
# "David was born in 1984"

sheldon = quip: -> "Bazinga!"

"I've always wanted to go to a goth club. {quip.toUpperCase}".format(sheldon)
# "I've always wanted to go to a goth club. BAZINGA!"
```

### String.prototype.format.transformers

“Transformers” can be attached to `String.prototype.format.transformers`:

```coffeescript
String::format.transformers.upper = (str) -> str.toUpperCase()

"Batman's preferred onomatopoeia: {0!upper}".format("pow!")
# "Batman's preferred onomatopoeia: POW!"
```

A transformer could sanitizing untrusted input:

```coffeescript
String::format.transformers.escape = (str) ->
  str.replace /[&<>"'`]/g, (chr) -> "&#" + chr.charCodeAt(0) + ";"

"<p class=status>{!escape}</p>".format("I <3 EICH")
# "<p class=status>I &#60;3 EICH</p>"
```

Or pluralize nouns, perhaps:

```coffeescript
String::format.transformers.s = (num) -> if num is 1 then "" else "s"

"{0}, you have {1} unread message{1!s}".format("Holly", 2)
# "Holly, you have 2 unread messages"

"{0}, you have {1} unread message{1!s}".format("Steve", 1)
# "Steve, you have 1 unread message"
```

String::format does not currently define any transformers.

### format(template, value1, value2, ..., valueN)

The module provides a format function when "required":

```coffeescript
format = require "string-format"

format("The name's {1}. {0} {1}.", "James", "Bond")
# "The name's Bond. James Bond."
```

`format(str, x, y, z)` is equivalent to `str.format(x, y, z)`.

### Creating reusable template functions

If a format string is used in multiple places, one could assign it to
a variable to avoid repetition. The idiomatic alternative is to create
a reusable template function via [`Function::bind`][2]:

```coffeescript
greet = String::format.bind "{0}, you have {1} unread message{1!s}"

greet("Holly", 2)
# "Holly, you have 2 unread messages"

greet("Steve", 1)
# "Steve, you have 1 unread message"
```

### Running the test suite

    make setup
    make test


[1]: http://docs.python.org/library/stdtypes.html#str.format
[2]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
