## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/blob/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/blob/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/blob/master/VII/README.md) 

# how to use metaclasses to create singleton classes in python 

First let's see how __call__ works with normal classes: 

```
>>> class A:
...  def __init__(self,a):
...   self.a = a
...  def __call__(self,a):
...   self.a = a
... 
>>> a = A(1)
>>> a.a
1
>>> a(42)
>>> a.a
42
>>> 
```

So, the __call__ provides us a way of reinitialising a class. So, when we try to initialse an instance the __call__ method is call. The `type` class also provides us a __call__ function which gets called every time you initialise a class. (Notice the difference b/w initialising and reinitialising)

```

>>> class S(type):
...  instance = None
...  def __call__(cls, *args, **kwargs): #cls is the class object A
...   import pdb; pdb.set_trace()
...   if cls.instance is None: 
...    cls.instance = super(S,cls).__call__(*args, **kwargs)
...   return cls.instance
... 
>>> class A(metaclass=S):
...  def __init__(self, a):
...   self.a = a
... 
>>> a = A(42)
> <console>(5)__call__()
(Pdb) cls
<class 'A'>
(Pdb) cls.instance
(Pdb) cls.instance is None 
True
(Pdb) n
> <console>(6)__call__()
(Pdb) cls.instance
(Pdb) n
> <console>(7)__call__()
(Pdb) cls.instance
<A object at 0x106f26240>
(Pdb) cls.instance.a
42
(Pdb) args
cls = <class 'A'>
args = (42,)
kwargs = {}
(Pdb) c
>>> a
<A object at 0x106f26240>
>>> a.a
42
>>> b = A()
> <console>(5)__call__()
(Pdb) cls.instance 
<A object at 0x106f26240>
(Pdb) c
>>> a is b 
True
>>> 

```

This approach will allow you to define a class in one file and then you can use instances of the class across different files maintaining dynamic global variables. 
