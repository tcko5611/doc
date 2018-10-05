[HOME](README.md)

# Basic

```
import argparse
parser = argparse.ArgumentParser(description='import file')
parser.add_argument('-file', metavar='datafile', type=str, required=True,
help='plot data file')
args = parser.parse_args()
print(args.file)
```

# add_argument
* *--file* and *-f* can be used in options
```
import argparse
parser = argparse.ArgumentParser(description='import file')
parser.add_argument('--file', '-f', metavar='datafile', type=str, required=True,
help='plot data file')
```
* *nargs* for number of element for the option
```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs=2)
>>> parser.add_argument('bar', nargs=1)
>>> parser.parse_args('c --foo a b'.split())
Namespace(bar=['c'], foo=['a', 'b'])
```

* *type* include *int*, *open*, *str*, *float*
```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('foo', type=int)
>>> parser.add_argument('bar', type=open)
>>> parser.parse_args('2 temp.txt'.split())
```

* *required*
```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', required=True)
>>> parser.parse_args(['--foo', 'BAR'])
Namespace(foo='BAR')
>>> parser.parse_args([])
usage: argparse.py [-h] [--foo FOO]
argparse.py: error: option --foo is required
```
* *metavar* for help information
```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', required=True)
>>> parser.parse_args(['--foo', 'BAR'])
Namespace(foo='BAR')
>>> parser.parse_args([])
usage: argparse.py [-h] [--foo FOO]
argparse.py: error: option --foo is required
```

# Parse_args()
```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x')
>>> parser.add_argument('--foo')
>>> parser.parse_args(['-x', 'X'])
Namespace(foo=None, x='X')
>>> parser.parse_args(['--foo', 'FOO'])
Namespace(foo='FOO', x=None)
```
