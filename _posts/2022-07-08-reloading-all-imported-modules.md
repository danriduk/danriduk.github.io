---
comments: true
layout: post
tags: maya python
---

## Reloading all imported modules

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

```python
def reload():
    from sys import modules
    import importlib

    my_modules = {}
    for key in modules.keys():
        if 'DRTools' in key:
            my_modules[key] = modules[key]

    for val in my_modules.values():
        importlib.reload(val)
```

<hr>

Example use case from the joint orient tool

```python
# MAYA MODULES
import maya.cmds as mc

# CUSTOM MODULES
from DRTools.utils import attrUtils

# RELOAD
from DRTools import reloadModules
reloadModules.reload()
```

This is now alot cleaner and easier to use rather then repetitive lines of importlib.reload()
It may be better to only reload modules that have been changed - but i do not deem that necessary for my use case.