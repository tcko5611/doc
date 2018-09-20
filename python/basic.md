[HOME](README.md)

# Variables
* str : 'aa'
* int : 10
* float : 10.0
* list : a = [1, 2, 3]
  * a.insert(2, 4) # [1,2,4,3]
  * a.appned(5) # [1, 2,4,3,5]
  * a.index(4) # 2
  * a.remove(3) # [1, 2, 4, 5]
  * a.reverse() # [5, 4, 2, 1]
  * a.sort() # [1, 2, 4, 5]
  * del a[0] # [2, 4, 5]
  * for b in a :...
* dict : a = {'a': 10, 'b': 20}
  * for k, v in a.items(): de...
  * for k in a.keys(): ...
  * for k in a.keys(): ...
  * a['c'] = 10
* tuple : a = ('a', 'b')
* set : a = {1, 2}
  * for b in a :...

# Input
* read from command line
  * use sys.argv
  ```
  import sys
  print "This is the name of the script: ", sys.argv[0]
  print "Number of arguments: ", len(sys.argv)
  print "The arguments are: " , str(sys.argv)
  ```
  * use argparse module
  ```
  import argparse
  parser = argparse.ArgumentParser(description='import file')
  parser.add_argument('-file', metavar='datafile', type=str, required=True,
                        help='plot data file')
  args = parser.parse_args()
  print(args.file)
  ```
* read from stdin
```
import sys
for line in sys.stdin:
    new_list = [int(elem) for elem in line.split()]
    list_of_lists.append(new_list)
```
* read from file
```
with open(fname) as f:
    content = f.readlines()
with open(fname) as f:
    for line in f:
	  ...
```

# Output
* output to stdout
```
print('{} {}'.format('aaa','bbb')) # aaa bbb
print('{:4d}{:4d}'.format(123,456)) # 123 456
print('{:6.2f}'.format(3.141592654)) #  3.14
```
* output to file
```
f = open(fname, 'w')
f.write(str)
```

# if statements
```
if x < 0:
  x = 0
  print('Negative changed to zero')
elif x == 0:
  print('Zero')
elif x == 1:
  print('Single')
else:
  x =0
```

# for statements 
```
for w in words:
  print(w, len(w))
```

# Module
fibo.py
```
# Fibonacci numbers module

def fib(n):    # write Fibonacci series up to n
    a, b = 0, 1
    while a < n:
        print(a, end=' ')
        a, b = b, a+b
    print()

def fib2(n):   # return Fibonacci series up to n
    result = []
    a, b = 0, 1
    while a < n:
        result.append(a)
        a, b = b, a+b
    return result
```
code
```
import fibo
>>> fibo.fib(1000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
>>> fibo.fib2(100)
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
>>> fibo.__name__
'fibo'
```

# Class
```
base, form = uic.loadUiType('mainwindow.ui')
class MainWindow(base, form):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
  ....
```

# PyQt 
* direct include mainwinodw.ui
```
base, form = uic.loadUiType('mainwindow.ui')
class MainWindow(base, form):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
	self.setupUi(self)
```
* using pyuic5 and include Ui_mainwindow.py
  * in Makefile
  ```
  pyuic5 mainwindow.ui -o Ui_mainwindow.py
  ```
  * in py file
  ```
  from Ui_mainwindow import Ui_MainWindow
  class MainWindow(QMainWindow, Ui_MainWindow):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
    self.setupUi(self)
  ```
