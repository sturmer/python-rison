# Prison, a Python encoder/decoder for Rison

## Quickstart

```bash
$ pip install prison
```

```python
>>> import prison
>>> prison.dumps({'foo': 'bar'})
'(foo:bar)'
>>> prison.loads('(foo:bar)')
{'foo': 'bar'}
```

## Rison - Compact Data in URIs

This page describes *Rison*, a data serialization format optimized for compactness in URIs. Rison is a slight variation of JSON that looks vastly superior after URI encoding. Rison still expresses exactly the same set of data structures as JSON, so data can be translated back and forth without loss or guesswork. 

You can skip straight to some [examples](#examples), or read on for more background. 

### Why another data serialization format?

Rison is intended to meet the following goals, in roughly this order:

- Comply with [URI specifications](http://gbiv.com/protocols/uri/rfc/rfc3986.html) and usage
- Express **nested** data structures
- Be **human-readable**
- Be **compact**

Rison is necessary because the obvious alternatives fail to meet these goals:

- URI-encoded XML and JSON are illegible and inefficient.
- [HTML Form encoding](http://www.w3.org/TR/html4/interact/forms.html#form-content-type) rules the web but can only represent a flat list of string pairs.
- Ian Bicking's [FormEncode](http://formencode.org/) package includes the [variabledecode](http://formencode.org/Validator.html#id16) parser, an interesting convention for form encoding that allows nested lists and dictionaries. However, it becomes inefficient with deeper nesting, and allows no terminal datatypes except strings.

Note that these goals are shaped almost entirely by the constraints of URIs, though Rison has turned out to be useful in command-line tools as well. In the *body* of an HTTP request or response, length is less critical and URI encoding can be avoided, so JSON would usually be preferred to Rison.

Given that a new syntax is needed, Rison tries to innovate as little as possible:

- It uses the same data model as, and a very similar syntax to [JSON](http://json.org/). The Rison grammar is only a slight alteration of the JSON grammar.
- It introduces very little additional quoting, since we assume that URI encoding will be applied on top of the Rison encoding.

### Differences from JSON syntax

  * no whitespace is permitted except inside quoted strings. 
  * almost all character escaping is left to the uri encoder. 
  * single-quotes are used for quoting, but quotes can and should be left off strings when the strings are simple identifiers. 
  * the `e+` exponent format is forbidden, since `+` is not safe in form values and the plain `e` format is equivalent. 
  * the `E`, `E+`, and `E` exponent formats are removed. 
  * object keys should be lexically sorted when encoding. the intent is to improve url cacheability. 
  * uri-safe tokens are used in place of the standard json tokens: 
    
    |rison token|json token|meaning      |
    |:----------|:---------|:------------|
    |`'`        |`"`       |string quote |
    |`!`        |`\`       |string escape|
    |`(...)`    |`{...}`   |object       |
    |`!(...)`   |`[...]`   |array        |
    
  * the JSON literals that look like identifiers (`true`, `false` and `null`) are represented as `!` sequences: 
    
    |rison token|json token|
    |:----------|:---------|
    |`!t`       |`true`    |
    |`!f`       |`false`   |
    |`!n`       |`null`    |

The `!` character plays two similar but different roles, as an escape character within strings, and as a marker for special values. This may be confusing.

Notice that services can distinguish Rison-encoded strings from JSON-encoded strings by checking the first character. Rison structures start with `(` or `!(`. JSON structures start with `[` or `{`. This means that a service which expects a JSON encoded object or array can accept Rison-encoded objects without loss of compatibility.

### Interaction with URI %-encoding

Rison syntax is designed to produce strings that be legible after being [form-encoded](http://www.w3.org/TR/html4/interact/forms.html#form-content-type) for the [query](http://gbiv.com/protocols/uri/rfc/rfc3986.html#query) section of a URI. None of the characters in the Rison syntax need to be URI encoded in that context, though the data itself may require URI encoding. Rison tries to be orthogonal to the %-encoding process - it just defines a string format that should survive %-encoding with very little bloat. Rison quoting is only applied when necessary to quote characters that might otherwise be interpreted as special syntax.

Note that most URI encoding libraries are very conservative, percent-encoding many characters that are legal according to [RFC
3986](http://gbiv.com/protocols/uri/rfc/rfc3986.html). For example, Javascript's builtin `encodeURIComponent()` function will still make Rison strings difficult to read. The rison.js library includes a more tolerant URI encoder.

Rison uses its own quoting for strings, using the single quote (**`'`**) as a string delimiter and the exclamation point (**`!`**) as the string escape character. Both of these characters are legal in uris. Rison quoting is largely inspired by Unix shell command line parsing.

All Unicode characters other than **`'`**` and **`!`** are legal inside quoted strings. This includes newlines and control characters. Quoting all such characters is left to the %-encoding process.

### Grammar

Modified from the [json.org](https://web.archive.org/web/20130910064110/http://json.org/) grammar.

- _object_
  - `()`
  - `(` _members_ `)`
- _members_
  - _pair_
  - _pair_ `,` _members_
- _pair_
  - _key_ `:` _value_
- _array_
  - `!()`
  - `!(` _elements_ `)`
- _elements_
  - _value_
  - _value_ `,` _elements_
- _key_
  - _id_
  - _string_
- _value_
  - _id_
  - _string_
  - _number_
  - _object_
  - _array_
  - `!t`
  - `!f`
  - `!n`
    <br>
    　　　　────────────
- _id_
  - _idstart_
  - _idstart_ _idchars_
- _idchars_
  - _idchar_
  - _idchar_ _idchars_
- _idchar_
  - any alphanumeric ASCII character
  - any ASCII character from the set `-` `_` `.` `/` `~`
  - any non-ASCII Unicode character
- _idstart_
  - any _idchar_ not in `-`, _digit_
    <br>
    　　　　────────────
- _string_
  - `''`
  - `'` _strchars_ `'`
- _strchars_
  - _strchar_
  - _strchar_ _strchars_
- _strchar_
  - any Unicode character except ASCII `'` and `!`
  - `!!`
  - `!'`
    <br>
    　　　　────────────
- _number_
  - _int_
  - _int_ _frac_
  - _int_ _exp_
  - _int_ _frac_ _exp_
- _int_
  - _digit_
  - _digit1-9_ _digits_
  - `-` digit
  - `-` digit1-9 digits
- _frac_
  - `.` _digits_
- _exp_
  - _e_ _digits_
- _digits_
  - _digit_
  - _digit_ _digits_
- _e_
  - `e`
  - `e-`

## Examples

## History
