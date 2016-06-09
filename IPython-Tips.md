## Set a breakpoint at a certain line in a file
```python
import ipdb
ipdb.set_trace()
```

## Drop into ipython shell when the running program raises an exception
Sometimes, a program will break deep in some complex loop and it can be hard to 
figure out where exactly you need to put the breakpoint.
```python
# insert this line at the top of any file that gets loaded as part of the 
# python program you want to debug
import sys
from IPython.core import ultratb
sys.excepthook = ultratb.FormattedTB(mode='Verbose',
    color_scheme='Linux', call_pdb=1)
```

## Define a custom magic function in the ipython shell
```ipython
def hello(line):
    print('Hello "{}"!'.format(line))

ip = get_ipython()
ip.define_magic('hi', lambda self, line: hello(line))
```
Now you can call your custom function like:
``hi world``
which outputs:
``Hello world!``
