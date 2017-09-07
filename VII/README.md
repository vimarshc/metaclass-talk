## Index
1. [classes are objects too](https://github.com/vimarshc/metaclass-talk/blob/master/I/README.md) 
2. [how to create a simple metaclass](https://github.com/vimarshc/metaclass-talk/blob/master/II/README.md) 
3. [using namespace in metaclasses](https://github.com/vimarshc/metaclass-talk/blob/master/III/README.md) 
4. [an awesome example using metaclass](https://github.com/vimarshc/metaclass-talk/blobl/master/IV/README.md) 
5. [how to use metaclasses to create singleton classes in python](https://github.com/vimarshc/metaclass-talk/blobl/master/V/README.md) 
6. [how django rest framework uses metaclasses](https://github.com/vimarshc/metaclass-talk/blobl/master/VI/README.md) 
7. [how django uses metaclasses to maintain backward compatability](https://github.com/vimarshc/metaclass-talk/blob/master/VII/README.md) 
# how django uses metaclasses to maintain backward compatability

For the last example of this talk let's go over how django uses metaclass to maintain backward compatability. This usage of metaclass was introduced into the source code as a result of this [issue](https://code.djangoproject.com/ticket/15363). This issue says that over time there was a change in the naming convention in django. Initially `get_queryset` used to be `get_query_set` being one of the examples. 

The conversation around this issue is also very interesting. The django community tried a lot of things. Amongst them was a decorator over the old function which raised a warning. But, people could still override the function rendering the decorator unused. 

[Here](https://github.com/django/django/blob/master/django/utils/deprecation.py#L33) is the metaclass that now handles the backward compatabilty issue.


It defines the new method if missing and warns about it, defines the old method if missing and complains whenever an old method is called.

[Here](https://github.com/django/django/blob/master/tests/deprecation/tests.py#L15) are the tests for the metaclass
