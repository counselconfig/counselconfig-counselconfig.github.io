---
layout: post
title: Automated unit testing
tags: Python Django testing
---

#  Python unit testing a web application inside of Django. 

The project looks like this:
<embed src='/testclasses.png' style='display: block; margin: 0 auto'>
_Class diagram

The objective here is to check that the following urls from urls.py return as expected. Django detects tests by looking for classes that also starts with 'test', hence the list on the right in the test folder:  


<style>
img {
  border: 1px solid #555;
}
</style>

<img src="/assets/images/unit_testing.png" alt="Award" width="100%" height="100%">

NB deleted the test.py file rgar Django automatically adds

I map all files to be tested to testing files - Django then looks for functions withinth the test classes. Here I am checking to see that my unit test virtual environment works, so I will intentionally fail a unit test:



```py
from django.test import TestCase
from django.urls import reverse, resolve 
from budget.views import project_list, project_detail, ProjectCreateView


class TestCheck(TestCase):

    def test_the_testsetup(self):
        #assert 1 == 2 # returns     assert 1 == 2 AssertionError

```

The ```self.assertEquals()``` verifies that the action of the function under test behaves as expected i.e. two arguments passed are equal. I am obtaining these results from my terminal like this:

<img src="/assets/images/unit_testing_terminal.png" alt="Award" width="100%" height="100%">


I am setting the URL now equal to the reverse of ```'list'```  - used to pass the url to the result function to what view Django is going to call.

```py
class TestUrls(TestCase):

    def list_resolves(self):
        url = reverse('list')
        print(resolve(url)) #use resolve function to pass the url

        """
        Creating test database for alias 'default'...
        System check identified no issues (0 silenced).
        ResolverMatch(func=budget.views.project_list, args=(), kwargs={}, url_name=list, app_names=[], namespaces=[])
        .
        ----------------------------------------------------------------------
        Ran 1 test in 0.007s

        OK
        """
        self.assertEquals(resolve(url).func, project_list)

        """
        ----------------------------------------------------------------------
        Ran 1 test in 0.008s

        OK
        """
```

Next we test the ``` add```  url:

```py

    def add_resolves(self):
        url = reverse('add')
        #self.assertEquals(resolve(url).func, ProjectCreateView) # ProjectCreateView is a class based view so throws an error below
        """
        ======================================================================
        FAIL: test_list_url_is_resolved (tests.test_urls.TestUrls)
        ----------------------------------------------------------------------
        Traceback (most recent call last):
        File "F:\Dominic\Python\django-testing-dslove\django-testing-dslove\budgetproject\budget\tests\test_urls.py", line 48, in test_list_url_is_resolved
            self.assertEquals(resolve(url).func, ProjectCreateView)
        AssertionError: <function ProjectCreateView at 0x0000023CFBE7EDC8> != <class 'budget.views.ProjectCreateView'>

        ----------------------------------------------------------------------
        Ran 1 test in 0.008s

        FAILED (failures=1)
        Destroying test database for alias 'default'...
```

This association error is correct since the function of ProjectCreateView is not equal to the class. This is because the view is class based so we can add ```func.view_class``` to resolve the error

```py
        
        self.assertEquals(resolve(url).func.view_class, ProjectCreateView)
        
        """
        ----------------------------------------------------------------------
        Ran 1 test in 0.005s

        OK
        Destroying test database for alias 'default'...
        """
```

An error that there is no reverse for details without any arguemnts if ```args``` is not used to equal some ``` slug```  in the following test function because the URL definition above needs one:

```py
    def detail_resolves(self):
        url = reverse('detail', args=['a-slug']) # args included as url definition uses a slug
        self.assertEquals(resolve(url).func, project_detail) 
        
        """
         ----------------------------------------------------------------------
        Ran 1 test in 0.007s

        OK
        Destroying test database for alias 'default'...
        """
```
