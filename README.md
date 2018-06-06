# python-rison

Python version of the rison encoder/decoder originally taken from http://mjtemplate.org/examples/rison.html, and forked from https://github.com/pifantastic/python-rison.

## Usage

```python
import prison

print prison.dumps({'foo': 'bar'})  # '(foo:bar)'

print prison.loads('(foo:bar)')  # {'foo': 'bar'}
```

## Tests

```bash
$ pip install nose
$ nosetests
```
