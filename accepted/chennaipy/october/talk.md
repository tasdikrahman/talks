<!-- $theme: gaia -->

<center>
<br><br>

###### Materials @ [https://github.com/prodicus/talks](https://github.com/prodicus/talks)
<br>

# Demystifying how imports work in Python

<br>

#### Tasdik Rahman ([@tasdikrahman](https://twitter.com/tasdikrahman/))

<br>

###### Presented @ ChennaiPy, October'16 meetup 
</center>

---

<!-- page_number: true -->

<br><br>

# Requirements

- Python 3.4 or newer.
- No extra 3rd party extensions needed.
- Coming over for this meetup!


---

# Modules

- Any `python` source file would be counted as a module. 

- You import a module to execute and access its classes, function definitions, attributes.

```python
>>> import os
>>> os.path.abspath('.')
'/home/tasdik/Dropbox/talks/chennaipy/october/samplecode'
>>> 
```
- `posixpath` would be the module name where the method `abspath()` resides. `posixpath` being the alias name for `os.path` in linux systems.

---

#### What happens when you import a module?

- It being a python script, the statements start getting executed from top to bottom of the source file.

- If there are any tasks in the statements ( eg: a `print()` statement ), then they get executed when the module is being imported.

```python
# 'samplecode/basicpackage/'
>>> import basicpackage.bar
inside basicpackage/__init__.py
inside 'basicpackage/bar'
>>>
```
---

# Packages

- Used to orgranize larger number of modules in a systematic manner.
- One way of accessing individual modules is using the `import foo.bar` import style.

```bash
# typical package structuring
$ tree basicpackage/
basicpackage
├── bar.py
├── foo.py
└── __init__.py
```

---

# Why packages?

```bash
bumblebee
├── constants.py
├── core.py
├── exceptions.py
├── helpers
│   ├── __init__.py
│   └── vwo_helpers.py
├── __init__.py
└── vwo
    ├── __init__.py
    └── smart_code.py
```

**Looks good?**

---

# Different ==styles== for importing modules

---

# from module import foo

- This essentially imports the module first then <span style="background-color:yellow;">picks up specific parts</span> from the module to be available locally.

```python
>>> from basicpackage import foo
inside basicpackage/__init__.py
inside 'basicpackage/foo.py' with a variable in it
>>>
```

- allows using the parts of the module without giving the full prefix before it.

---

# from module import *

- <span style="background-color:yellow;">Brings out all the symbols</span> from the module and makes them available in the namespace.

```python
>>> from basicpackage_all import *
inside basicpackage_all/__init__.py
inside 'basicpackage_all/foo.py' with a variable in it
inside 'basicpackage_all/bar.py'
>>>
```
- You can use `__all__` inside your `__init__.py` module to import the modules which you need.

- **Generally not a good idea!** Namespace collisions can occur.

---

# Takeaways so far

- The way you import a module doesn't actually change the working of the module.

- Difference between 
`import foo.bar` and `from foo import bar` ?
	 - the difference is subjective. Pick one style and be consistent with  it.
	- doing a `from foo import bar` is more efficient.
		- 	`python` imports the whole file! period.


---

# Module names

- naming modules follow the general variable naming convention.

```bash
# Bad choices
$ touch 2foo.py MyAwesomeFoo.py os.py

# Good choices
$ touch foo.py a_large_module_name.py
```

- Don't use Non-ASCII characters while doing so.
- Avoid creating module names which conflict with the standard library modules.

---

# Module lookup

- If it's not in the python path, it just won't import.

```python
>>> pprint(sys.path)
['',
 '/usr/lib/python35.zip',
 ...
 '/usr/lib/python3/dist-packages']

```

- Explicitly bring a module inside your path

```python
>>> import sys
>>> sys.path.append('/absoule/path/to/module')
```

---

## Modules get imported  ==Only once==!

---

<br><br>

## But I really want to import it again!

```python
>>> from importlib import reload
>>> reload(foo)
```

- This is generally not recommended!
- If you do so, zombies will spawn. 
- No really!

---

# Implicit Relative imports

```python
$ rod/
    foo.py
    bar.py
    __init__.py
```

- So want to have some things from `foo.py` inside `bar.py` ? Nothing uncommon.

```python
# python 2
# inside "bar.py"
import foo
```
- Don't do it! Works in `python2`  but doesn't work in `python3`

---

# How do I fix it?

---

# Absolute relative imports

- One way to fix it would be using the name of it's top level package name `relativeimports`.

```python
# relativeimports/foo.py
from relativeimports import bar

```

- This works, but is brittle!
- What if you wanted to change the name of the top level package?
- Errors!!!!

---

# Explicit relative imports

- A better way would be to 

```python
# explicitimports/bar.py
from . import foo
```

- Works even when you rename the root level package for whatever the reason may be 
(eg: you renamed it to `explicitimports_v1_0` )

```bash
$ mv explicitimports/ newimports/
```

---

<br>
<br>

The leading **(.)** would be used to move up a directory.

```python
# look for foo.py in the same level
from . import foo  

# go a dir up and import foo.py
from .. import foo  

# go a dir up and enter plino/ and look for bar.py
from ..plino import bar  
```

---

<center>

<!-- *template: invert -->

# `__init__.py`

![center](images/what-the-heck.jpg)

---

# What should you put into it?

- Most of the time, it's empty!
- Stiching together submodules:

```python
# minions/foo.py
class Foo(object):
	pass

# minions/bar.py
class Bar(object):
	pass
    
# minions/__init__.py
from .foo import Foo
from .bar import Bar
    
```

---

# Advantage?

- Headache free imports for small modules 

```python
>>> import minions
inside minions/__init__.py
inside 'minions/foo.py' with a variable in it
inside 'minions/bar.py'
>>> a = minions.Foo()
>>> b = minions.Bar()
```

- controlling import behaviour of `from foo import *` using the `__all__` variable inside `__init__.py`

---

# Performance anybody?

- Should I put the whole python package inside the `__init__.py`?
- Yes. If it's small!
- Not a good idea if you have a very large project! 

---

# References

- https://docs.python.org/3/tutorial/modules.html
- https://docs.python.org/3/reference/import.html
- https://docs.python.org/3/reference/executionmodel.html
- https://docs.python.org/3/library/distribution.html

---

<center>

<br><br>
## ==Questions==? 
## Would be happy to answer them!

<br><br>

#### http://tasdikrahman.me/

###### Twitter ([@tasdikrahman](https://twitter.com/))

###### Github ([@prodicus](https://github.com/prodicus))