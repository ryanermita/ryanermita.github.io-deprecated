---
title: "Python Mock and MagicMock"
date: "2019-02-04"
template: "post"
draft: false
slug: "python-mock-and-magicmock"
category: "Software Engineering"
tags:
  - "Python"
  - "unittest"
  - "Mock"
  - "MagicMock"
description: "While I’m looking for a way to test my Python function that has a pymysql query, I stumble upon a code snippet with mock.MagicMock(). This pique my curiosity how it differs with mock.Mock() in Python. This article will discuss the difference between the two and when to use one over the other."
---

While I’m looking for a way to test my function that has a pymysql query, I stumble upon a [code snippet](https://stackoverflow.com/q/39227681) with this line. (the same time I saw [python autospeccing](https://medium.com/ryans-dev-notes/python-autospeccing-72c2a5ba5e28)).

```
mock_cursor = mock.MagicMock()
```

I always use `Mock` when I do [unit test in python](https://medium.com/ryans-dev-notes/unit-testing-with-python-unittest-module-c37531e28d75), and its my first time to see a `MagicMock`. So it got me curious, what is the difference between the two and when to use one over the other.

## Mock and MagicMock
[`Mock`](https://docs.python.org/3.5/library/unittest.mock.html#unittest.mock.Mock) is use for replacing or mocking an object in python unittest while [`MagicMock`](https://docs.python.org/3.5/library/unittest.mock.html#unittest.mock.MagicMock) is a subclass of Mock with all the [magic methods](https://dbader.org/blog/python-dunder-methods) pre-created and ready to use. These are the pre-created magic methods and its default values for `MagicMock`.

```
__lt__: NotImplemented
__gt__: NotImplemented
__le__: NotImplemented
__ge__: NotImplemented
__int__: 1
__contains__: False
__len__: 0
__iter__: iter([])
__exit__: False
__complex__: 1j
__float__: 1.0
__bool__: True
__index__: 1
__hash__: default hash for the mock
__str__: default str for the mock
__sizeof__: default sizeof for the mock
```

Below is an example of `Mock` trying to use one of the magic method pre-created in `MagicMock`.

```
import unittest
from unittest.mock import Mock

class TestMock(unittest.TestCase):
    def test_mock(self):
        mocked_object = Mock()
        print(len(mocked_object))  # magic method
        pass

result:
test_mock (test_func.TestMock) ... 

ERROR====================================================================
ERROR: test_mock (test_func.TestMock)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/ryan/Workspace/playground/test_func.py", line 9, in test_mock
    print(len(mocked_object))
TypeError: object of type 'Mock' has no len()
----------------------------------------------------------------------
Ran 1 test in 0.001s
FAILED (errors=1)
```

Below is an example of `MagicMock` trying to use one of its pre-created magic method.

```
import unittest
from unittest.mock import MagicMock

class TestMagicMock(unittest.TestCase):

    def test_magicmock(self):
        mocked_object = MagicMock()
        print(len(mocked_object))  # magic method
        pass

result:
test_mock (test_func.TestMagicMock) ... 0
ok
--------------------------------------------------------------------
Ran 1 test in 0.001s
OK
```

And below is an example on how can we create those magic methods in `Mock` manually.

```
import unittest
from unittest.mock import Mock

class TestMock(unittest.TestCase):
    def test_mock(self):
        mocked_object = Mock()    #manually create a magic method    
        mocked_object.__len__ = Mock(return_value=1)
        print(len(mocked_object))  # magic method
        pass
    
result:
test_mock (test_func.TestMockAndMagicMock) ... 0
ok
-------------------------------------------------------------------
Ran 1 test in 0.000s
OK
```

&nbsp;
## Application

If `MagicMock` already creates the magic methods automatically, what is the use of `Mock`? Why not just upgrade the `Mock` class with pre-created magic methods? and what scenarios can I use Mock and MagicMock? Luckily, someone already ask that [question on stackoverflow](https://stackoverflow.com/questions/17181687/mock-vs-magicmock) and it is a pretty good answer. The answer is quoted from [Michael Foord](https://twitter.com/voidspace) the author of `Mock`.

  **Q**: Why was MagicMock made a separate thing rather than just folding the ability into the default mock object?

  **A**: One reasonable answer is that the way MagicMock works is that it preconfigures all these protocol methods by creating new Mocks and setting them, so if every new mock created a bunch of new mocks and set those as protocol methods and then all of those protocol methods created a bunch more mocks and set them on their protocol methods, you’ve got infinite recursion…

  What if you want accessing your mock as a container object to be an error — you don’t want that to work? If every mock has automatically got every protocol method, then it becomes much more difficult to do that. And also, MagicMock does some of this preconfiguring for you, setting return values that might not be appropriate, so I thought it would be better to have this convenience one that has everything preconfigured and available for you, but you can also take a ordinary mock object and just configure the magic methods you want to exist…

  The simple answer is: just use MagicMock everywhere if that’s the behavior you want.

## Resources

- [Python Documentation: MagicMock](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.MagicMock)
- [Python Documentation: Mock](https://docs.python.org/3.5/library/unittest.mock.html#the-mock-class)
- [Michael Foord, Testing with Mock, PyCon2011 Atlanta](https://pyvideo.org/pycon-us-2011/pycon-2011--testing-with-mock.html)
- [StackOverflow’s Mock vs MagicMock](https://stackoverflow.com/q/17181687)
- [Enriching Your Python Classes With Dunder (Magic, Special) Methods](https://dbader.org/blog/python-dunder-methods)