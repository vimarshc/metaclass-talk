# django-example

For the last example of this talk let's go over how django uses metaclass to maintain backward compatability. This usage of metaclass was introduced into the source code as a result of this [issue](https://code.djangoproject.com/ticket/15363). This issue says that over time there was a change in the naming convention in django. Initially `get_queryset` used to be `get_query_set` being one of the examples. 

The conversation around this issue is also very interesting. The django community tried a lot of things. Amongst them was a decorator over the old function which raised a warning. But, people could still override the function rendering the decorator unused. 

[Here](https://github.com/django/django/blob/master/django/utils/deprecation.py#L33) is the metaclass that now handles the backward compatabilty issue.


It defines the new method if missing and warns about it, defines the old method if missing and complains whenever an old method is called.

[Here](https://github.com/django/django/blob/master/tests/deprecation/tests.py#L15) are the tests for the metaclass
