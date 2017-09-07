## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/blob/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/blob/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/blob/master/VII/README.md) 
 

# using namespace in metaclasses


Let's go over the second step of when a class object is being created. 

> Once the appropriate metaclass has been identified, then the class namespace is prepared. If the metaclass has a __prepare__ attribute, it is called as namespace = metaclass.__prepare__(name, bases, **kwds) (where the additional keyword arguments, if any, come from the class definition).


If you recall the third argument in the type initiation and the __new__ function; attrs (The dictionary), the namespace allows us the modify this dictionary. In the above once  `namespace = metaclass.__prepare__(name, bases, **kwds)` is called all the attributes passed in the attrs parameter are attached to the namepspace variable. Access to this process and we can change the dictionary to a OrderedDict or even override the dict class to add custom behaviour as we can see in the following examples: 



```
>>> class OrderedClass(type):
... 
...  @classmethod
...  def __prepare__(metacls, name, bases, **kwds):
...   return collections.OrderedDict()
...  def __new__(cls, name, bases, namespace, **kwds):
...   result = type.__new__(cls, name, bases, dict(namespace))
...   result.members = tuple(namespace)
...   return result
... 
>>> class A(metaclass=OrderedClass):
...     def one(self): pass
...     def two(self): pass
...     def three(self): pass
...     def four(self): pass
... 

```




```
>>> # The custom dictionary
>>> class member_table(dict):
...     def __init__(self):
...         self.member_names = []
...     def __setitem__(self, key, value):
...         if key not in self:
...             self.member_names.append(key)
...         dict.__setitem__(self, key, value)
... 
>>> # The metaclass
>>> class OrderedClass(type):
...     @classmethod
...     def __prepare__(metacls, name, bases): # No keywords in this case
...         return member_table()
...     def __new__(cls, name, bases, classdict):
...         result = type.__new__(cls, name, bases, classdict)
...         result.member_names = classdict.member_names
...         return result
... 
>>> 
>>> class MyClass(metaclass=OrderedClass):
...     def method1(self):
...         pass
...     def method2(self):
...         pass
... 
>>> a = MyClass()
>>> a.member_names
['__module__', '__qualname__', 'method1', 'method2']
>>> 

```
