## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/blob/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/blob/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/blob/master/VII/README.md) 


# an awesome example using metaclass


Most people must be familiar with the json.dumps function. 

```
>>> import json
>>> json.dumps({'one':1})
'{"one": 1}'
>>> 

```
As per the documentation json.dumps takes as parameter the value it has to dump.
Now, what I can achieve with metaclass is I can introduce dumps method into my own class. 

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

In the above function when dumps is attached to the class, the function becomes a method with first parameter as `self`. So, similarily I can attach the print function and whatever function I wish to. I can import some function from somewhere and just attach it to my class. If you are making a library this approach will allow your user to not care about the definition of the method. 
