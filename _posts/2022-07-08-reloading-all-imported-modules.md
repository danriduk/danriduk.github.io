---
comments: true
layout: post
tags: maya python
---

As part of the on-going project to build a library of rigging tools i wanted a better way to reload all imported custom modules


In python 3 we now use importlibs reload() function for reloading
```python
import importlib
importlib.reload(MODULE)
```

<hr>

using sys.modules we can get a dictionary of all current imported modules with the key being the module name and value being the path to the module.
```python
# {..., 'DRTools.utils.attrUtils' from 'C:\\Users/danri/Documents/maya/2023/scripts\\DRTools\\utils\\attrUtils.py'>, ...}
```
Utilizing this we can only reload modules in a specific directory where our custom tools are located and reuse the function throughout our codebase.
A neat trick to reload everything is to simply delete the key from the sys.modules dictionary, that way we we will be re-importing all the modules in our codebase at once.
Avoiding a bunch of importlib.reload(MODULE)'s and chaining them together in our codebase.

```python
def reload_DRig():
    modules_to_del = []
    for module in sys.modules.copy():
        # print(module)
        top_module = module.split('.')[0]
        if top_module == 'DRig':
            modules_to_del.append(module)

    for module in modules_to_del:
        del(sys.modules[module])
```

<hr>

Example use case from the joint orient tool

```python
# RELOAD
from DRig.utils import pyUtils
pyUtils.reload_DRig()
```

This is now alot cleaner and easier to use rather then repetitive lines of importlib.reload()
It may be better to only reload modules that have been changed - but i do not deem that necessary for my use case.