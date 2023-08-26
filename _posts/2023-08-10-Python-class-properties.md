---
comments: true
layout: post
tags: maya python rigging
---

A powerful feature of classes in python is the use of property attributes using the `@property` decorator.
I am using this feature extensively when building my rigging tools so here is a quick outline of how they work, and how powerful they are.

<hr>
Here is a simple class with 2 attributes side and name

```python
class Arm():
    def __init__(self, side='c', name='arm'):
        self.side = side
        self.name = name
```

we can grab these attributes fairly easily

```python
arm = Arm()
arm.side # 'c'
arm.name # 'arm'
```

however, what if we wanted to grab the 'fullName' combining these attributes:

```python
class Arm():
    def __init__(self, side='c', name='arm'):
        self.side = side
        self.name = name

        self.fullname = f'{self.side}_{self.name}'
```

While this works, what if you wanted to change the side or name of the module after initialisation ?

```python
arm = Arm()
arm.side # 'c'
arm.name # 'arm'
arm.fullname # 'c_arm'

arm.side = 'l'
arm.name = 'armA'
arm.side # 'c'
arm.name # 'arm'
arm.fullname # 'c_arm'
```

since the full name is initalised at the objects creation, it wont update with the new name we give it if we decide to later.
Introducing properties `@property`

```python
class Arm():
    def __init__(self, side='c', name='arm'):
        self.side = side
        self.name = name

    @property
    def fullname(self):
        return f'{self.side}_{self.name}'
```

Here we can use the `@property` decorator on the fullname attribute and it will essentially run the function whenever we call it, thus returning the new name if it changes !
