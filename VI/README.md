#drf-example

If you have been able to follow till here, the DRF example will be a peice of cake.

DRF uses metaclass in the [Serializer](https://github.com/encode/django-rest-framework/blob/master/rest_framework/serializers.py#L349) over (here)[https://github.com/encode/django-rest-framework/blob/master/rest_framework/serializers.py#L288].

What it is doing is when a serializer class is initiated inside the __new__ function a new attribute is added to the dictionary of attributes. 

Let's run some tests in [DRF](https://github.com/vimarshc/django-rest-framework/blob/dd2230cd8baf08d3cc645eb6c7098fb52122f001/tests/test_serializer.py#L119) and place a deugger so that we can get the results of the modifications introduced by `metaclass`:
```
((venv-drftest)Vimarshs-MacBook-Pro:django-rest-framework vimarshchaturvedi$ ./runtests.py test_valid_serializer
================================================================================ test session starts ================================================================================
platform darwin -- Python 2.7.10, pytest-3.0.5, py-1.4.34, pluggy-0.4.0
rootdir: /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework, inifile: tox.ini
plugins: cov-2.4.0, django-3.1.2
collected 1076 items 

tests/test_serializer.py 
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> PDB set_trace (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(120)test_valid_serializer()
-> serializer = self.Serializer(data={'char': 'abc', 'integer': 123})
(Pdb) n
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(121)test_valid_serializer()
-> assert serializer.is_valid()
(Pdb) serializer._declared_fields
OrderedDict([('char', CharField()), ('integer', IntegerField())])
(Pdb) 

```


The second piece of snippet inside the function makes sure that the fields in the classes it is inheriting are also included. Let's [see](https://github.com/vimarshc/django-rest-framework/blob/bf686cf4f6309b8757ea7b20f9f4bcd710bbe003/tests/test_serializer.py#L121) 
```
(venv-drftest)Vimarshs-MacBook-Pro:django-rest-framework vimarshchaturvedi$ ./runtests.py test_valid_serializer
================================================================================ test session starts ================================================================================
platform darwin -- Python 2.7.10, pytest-3.0.5, py-1.4.34, pluggy-0.4.0
rootdir: /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework, inifile: tox.ini
plugins: cov-2.4.0, django-3.1.2
collected 1076 items 

tests/test_serializer.py 
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> PDB set_trace (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(121)test_valid_serializer()
-> serializer = self.Serializer(data={'char': 'abc', 'integer': 123})
(Pdb) n
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(122)test_valid_serializer()
-> assert serializer.is_valid()
(Pdb) serializer._declared_fields
OrderedDict([('char', CharField()), ('integer', IntegerField())])
(Pdb) c
.

=============================================================================== 1075 tests deselected ===============================================================================
==================================================================== 1 passed, 1075 deselected in 32.95 seconds =====================================================================



```


To finish off this example let's try to put a debugger inside the [metaclass](https://github.com/vimarshc/django-rest-framework/blob/69f93e00bac5b519f12a4214711732c66ddfb041/tests/test_serializer.py#L102)


```
tests/test_serializer.py 
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> PDB set_trace (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(103)__new__()
-> attrs['_declared_fields'] = cls._get_declared_fields(bases, attrs)
(Pdb) attrs['_declared_fields']
*** KeyError: '_declared_fields'
(Pdb) n
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(104)__new__()
-> return super(_SerializerMetaclass, cls).__new__(cls, name, bases, attrs)
(Pdb) attrs['_declared_fields']
OrderedDict([('char', CharField())])
(Pdb) c

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> PDB set_trace (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(103)__new__()
-> attrs['_declared_fields'] = cls._get_declared_fields(bases, attrs)
(Pdb) n
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(104)__new__()
-> return super(_SerializerMetaclass, cls).__new__(cls, name, bases, attrs)
(Pdb) attrs['_declared_fields']
OrderedDict([('char', CharField()), ('integer', IntegerField())])
(Pdb) c

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> PDB set_trace (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
> /Users/vimarshchaturvedi/Desktop/reading/drf/django-rest-framework/tests/test_serializer.py(122)test_valid_serializer()
-> serializer = self.Serializer(data={'char': 'abc', 'integer': 123})
(Pdb) c
.

```



