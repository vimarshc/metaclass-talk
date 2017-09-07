## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/blob/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/blobl/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/blobl/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/blobl/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/blob/master/VII/README.md) 

# how to create a simple metaclass

Let's first have a look at what python's documentation says about using the __metaclass__:


**When a class definition is executed, the following steps occur:**
 * **the appropriate metaclass is determined**
 * **the class namespace is prepared**
 * **the class body is executed**
 * **the class object is created**

When we use the `class` keyword the first thing that python does is that it tries to determine what class should be used to initialse the class object. When we create a class w/o specifying the __metaclass__ attribute it falls back on the `type` class. 

So, we can either pass a function to the metaclass attribute: 
```
>>> def modify_foo(name, bases, attr):
...  new_attr = {}
...  for key, value in attr.items():
...   if key == 'foo':
...    new_attr['_foo'] = attr.get('foo')
...   else:
...    new_attr[key] = attr.get(key)
...  return type(name, bases, new_attr)
... 
>>> class A(metaclass=modify_foo):
...  foo = 42
... 
>>> a = A()
>>> hasattr(a,'foo')
False
>>> hasattr(a,'_foo')
True
>>> getattr(a,'_foo')
42
>>> getattr(a,'foo')
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: 'A' object has no attribute 'foo'
>>> 
```


Or we can pass a class. Since the `type` is also a class we can define a class which inherits `type` and override some methods and add some extra behaviour. 

```
>>> class A(type):
...  def __new__(cls, name, bases, attr):
...   new_attr = {}
...   for key, value in attr.items():
...    if key == 'foo':
...     new_attr['_foo'] = attr.get('foo')
...    else:
...     new_attr[key] = value
...   return super(A,cls).__new__(cls, name, bases, new_attr)
... 
>>> class B(metaclass=A):
...  foo=42
... 
>>> b = B()
>>> getattr(b, '_foo')
42
>>> getattr(b, 'foo')
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: 'B' object has no attribute 'foo'
>>> 
```

The __new__ function is the method that is called right before __init__. The last three parameters are the same ones that we pass to type while creating a class (name, base classes and dictionary of attributes). And the first is the class object itself. The differenc between the __new__ method and __init__ method is that the latter is called when the class object has been initialised and __new__ is called before. The first argument in __new__ and __init__ is the class object and instance object respectively. 

Via the __new__ method we can modify the variable and method names via making changes in the dictionary while when __init__ is called the attributes defined in the namespace are already defined on the class instance. 
