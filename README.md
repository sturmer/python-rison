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


## Examples

## History
