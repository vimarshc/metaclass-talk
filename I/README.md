## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/edit/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/edit/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/edit/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/edit/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/edit/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/edit/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/edit/master/VII/README.md) 

# classes are objects too

### A small re-introduction over what classes are in Python 

While I was researching material for this talk I stumbled upon this image for the query: What are classes? 


![alt text](https://raw.githubusercontent.com/vimarshc/metaclass-talk/master/I/classes-biscuits.jpg)

And as those who know how classes behave will agree that this is a pretty accurate analogy. If we go over the steps: 
- Prepare a dough
- Fill the dough into the containers
- Bake

And we have instances of biscuits. 

If I were to describe what metaclasses do via this analogy it would be, while the biscuits were in the over I open the door and sprinkle some sugar over the bascuits while they're in the ovemn and the final instances have some changes which were introduced at runtime. 

But let's not get ahead of ourselves and first let's go over how Python 3's [documentation](https://docs.python.org/3/tutorial/classes.html) define's classes: 


> Classes provide a means of bundling data and functionality together. Creating a new class creates a new type of object, allowing new instances of that type to be made. Each class instance can have attributes attached to it for maintaining its state. Class instances can also have methods (defined by its class) for modifying its state.


Classes act like templates for the instances that are created via their respective classes. But what this piece of documentation also subtly points out that classes in Python are a bit more than just templates. They themselves are objects as well. 

Let's see what that allows us to do: 

# A basic class example in Python
```
>>> class FirstClass(object):
...		pass
...

>>> first_instance = FirstClass()		 			
>>> print(first_instance)
<__main__.FirstClass object at 0x8974f2c>

```

When we used the `class` statement it creates an object with the name of FirstClass

Since FirstClass itself is an object like first_instance we can: 
- Assign it to a variable 
- Copy it
- Add attributes to it
- Pass it to a parameter

Let's have a look:

```
>>> print(FirstClass) # you can print a class because it's an object
<class 'FirstClass'>
>>> def echo(o):
...       print(o)
... 
>>> echo(FirstClass) # you can pass a class as a parameter
<class 'FirstClass'>
>>> print(hasattr(FirstClass, 'new_attribute'))
False
>>> FirstClass.new_attribute = 42
>>> print(hasattr(FirstClass, 'new_attribute'))
True
>>> print(FirstClass.new_attribute)
42
>>> _FirstClass = FirstClass # you can assign a class to a variable
>>> print(_FirstClass.new_attribute)
42
>>> print(ObjectCreatorMirror())
<class 'FirstClass'>

```

Along with that I can create a class dynamically as well: 

```
>>> def choose_class(name):
...     if name == 'foo':
...         class Foo(object):
...             pass
...         return Foo 
...     else:
...         class Bar(object):
...             pass
...         return Bar
...     
>>> MyClass = choose_class('foo') 
>>> print(MyClass) # the function returns a class, not an instance
<class 'Foo'>
>>> print(MyClass()) # you can create an object from this class
<__main__.Foo object at 0x89c6d4c>

```	

### A class is an instance of type


As we saw in the previous example that classes themselves are objects, i.e classes are instances of something themselves. 
Let's see which class are the class objects that we have created above are instances of. 

```
>>> class A:
...  pass
... 
>>> A
<class 'A'>
>>> type(A)
<class 'type'>
>>> 

```

![alt text](https://raw.githubusercontent.com/vimarshc/metaclass-talk/master/I/class-is-instance.png)

All class objects are instances of the class `type`. Using the biscuits example, as the steel container is the template for the biscuit, steel is the template for the steel container. 

Now let's try to create a class object using `type` class or we can say let's create instances of the `type` class. 

```
# Let's see what happends under the syntax sugar of: 
# class A: 
#     pass


>>> A = type('A', (), {})
>>> x = A()
>>> y = type('A', (), {})()
>>> x
<A object at 0x106bf4320>
>>> y
<A object at 0x106bf4470>
>>> 


>>> print (type('A', (), {})())
<A object at 0x106bfc550>
>>> print (type('A', (), {}))
<class 'A'>
>>> 

```

Under the hood we initialse the class type, the resulting instance gives us a class. The parameters that we pass are as follows: 
- String: Name of the class
- Tuple: A tuple of base classes which we want the class to inherit
- Dictinoary: Definfing the variables and methods we want inside the class. 

Let's try giving a base class and some variables. 
```

>>> class A:
...  x = 1
...  def print_hey(self):
...   print ('Hey')
... 
>>> class B:
...  def print_foo(self):
...   print('Foo')
>>> def print_hey(self):
...  print('Duh Doy')
... 
>>> X = type('X', (A,B), {'print_hey': print_hey, 'y':42})
>>> i = X()
>>> i.x
1
>>> i.y
42
>>> i.print_hey()
Duh Doy
>>> i.print_foo()
Foo
```

I have created two classes, given some properties (variable and methods to both). I have created a class via type passing base classes for the class to inherit and passed some variables and overriden a method and the class that we have behaves pretty much how a normal class would behave. 

