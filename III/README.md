#using-namespace


Let's go over the second step of when a class object is being created. 

> Once the appropriate metaclass has been identified, then the class namespace is prepared. If the metaclass has a __prepare__ attribute, it is called as namespace = metaclass.__prepare__(name, bases, **kwds) (where the additional keyword arguments, if any, come from the class definition).


Let's go over an example: 


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

And as always we can introduce custom behaviour by passing a dict which has been inherited and desired behaviour has been introduced.  

```
# The custom dictionary
class member_table(dict):
    def __init__(self):
        self.member_names = []
    def __setitem__(self, key, value):
        if key not in self:
            self.member_names.append(key)
        dict.__setitem__(self, key, value)

# The metaclass
class OrderedClass(type):
    @classmethod
    def __prepare__(metacls, name, bases): # No keywords in this case
        return member_table()
    def __new__(cls, name, bases, classdict):
        result = type.__new__(cls, name, bases, classdict)
        result.member_names = classdict.member_names
        return result

class MyClass(metaclass=OrderedClass):
    def method1(self):
        pass
    def method2(self):
        pass

```