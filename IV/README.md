#awesome-example#

json.dumps takes as parameter the value it has to serialize. 

```
>>> class _D(type):
...     def __init__(cls, name, bases, dict):
...         from json import dumps 
...         super(_D, cls).__init__(name, bases, dict)
...         setattr(cls, 'dumps', dumps)

>>> class A(dict, metaclass=_D):
...  pass
>>> a = A()
>>> a.__setitem__('one', 1)
>>> a.dumps()
'{"one": 1}'
>>> 

```

In the above function when dumps is attached to the class, the function becomes a method with first parameter as `self`.