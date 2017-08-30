Let's first have a look at what python's documentation says about using the __metaclass__:


>When a class definition is executed, the following steps occur:

>the appropriate metaclass is determined
>the class namespace is prepared
>the class body is executed
>the class object is created


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

	The __new__ function is the method that is called right before __init__. The last three parameters are the name ones that we pass to type while creating a class. And the first is the class object itself. The differenc between the __new__ method and __init__ method is that the latter is called when the class object has been initialised and __new__ is called before. The __new__ function is supposed to return an object while the __init__ method is only used for initialising new variables and methods. With __init__ you cannot rename the class variables and names. 