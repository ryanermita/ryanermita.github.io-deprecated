---
title: "Python Autospeccing"
date: "2019-02-03"
template: "post"
draft: false
slug: "python-autospeccing"
category: "Software Engineering"
tags:
  - "Python"
  - "unittest"
  - "Autospeccing"
description: "While I’m looking for a way to test my function that has a pymysql query, I stumble upon a code snippet with @mock.patch('simple.pymysql', autospec=True). So I took my time to know how this stuff works. Apparently Python has a very good documentation about it. This article summarizes what I understand about Python Autospec."
---

While I’m looking for a way to test my function that has a pymysql query, I stumble upon a code snippet with this line.

```
@mock.patch('simple.pymysql', autospec=True)
```

So I took my time to know how this stuff works. Apparently Python has a very good documentation about it.

## What’s `autospec=True` for?

  If you set `autospec=True` then the mock will be created with a spec from the object being replaced. All attributes of the mock will also have the spec of the corresponding attribute of the object being replaced. Methods and functions being mocked will have their arguments checked and will raise a `TypeError` if they are called with the wrong signature. — [Python Patch documentation](https://docs.python.org/3.5/library/unittest.mock.html#patch)

To make it short, `autospec=True` creates a mock object with the spec of the object being mocked. It is `False` by default. You can also create a autospecced Mock object using `create_autospec(spec)`.

```
import unittest
from unittest.mock import patch

class MyDummyClass:

    def test_dummy_function(self):
        return "hello"


class TestAutoSpec(unittest.TestCase):

    @patch('test_func.MyDummyClass', autospec=True)
    def test_autospec_true(self, mock_dummy_class):
        print(dir(mock_dummy_class))
        pass

    @patch('test_func.MyDummyClass')
    def test_autospec_false(self, mock_dummy_class):
        print(dir(mock_dummy_class))
        pass
```

The sample test script above will have this output below.

```
python -m unittest test_func.py -v
test_autospec_false (test_func.TestAutoSpec) ... ['assert_any_call', 'assert_called', 'assert_called_once', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'assert_not_called', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect']
ok
test_autospec_true (test_func.TestAutoSpec) ... ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'assert_any_call', 'assert_called', 'assert_called_once', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'assert_not_called', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect', 'test_dummy_function']
ok
```

As you can notice with the result, the test method with `autospec=True` has additional `test_dummy_function` and [dunder/magic methods](https://dbader.org/blog/python-dunder-methods). Those additional functions are part of the spec of `MyDummyClass`.

## Why does autospeccing exist?
Based on the Python documentation autospeccing solves these two flaws of Mocking objects.

- **Because mocks auto-create attributes on demand**, and allow you to call them with arbitrary arguments, if you misspell one of these assert methods then your assertion is gone.
- The second issue is more general to mocking. If you refactor some of your code, rename members and so on, **any tests for code that is still using the old API but uses mocks instead of the real objects will still pass**. This means your tests can all pass even though your code is broken.

If Mocking have those flaws and autospeccing solves it. Why not just create Mock objects with `autospec=True` by default instead of `False`? The reason behind this is also written in Python Documentation:

  This isn’t without caveats and limitations however, which is why it is not the default behaviour. In order to know what attributes are available on the spec object, autospec has to introspect (access attributes) the spec. As you traverse attributes on the mock a corresponding traversal of the original object is happening under the hood. If any of your specced objects have properties or descriptors that can trigger code execution then you may not be able to use autospec. On the other hand it is much better to design your objects so that introspection is safe.

  A more serious problem is that it is common for instance attributes to be created in the [`__init__()`](https://docs.python.org/3.5/reference/datamodel.html#object.__init__) method and not to exist on the class at all. **autospec can’t know about any dynamically created attributes and restricts the API to visible attributes.**

This is a simple code snippet that demonstrate the quoted caveat above.

```
import unittest
from unittest.mock import patch


class MyDummyClass:

    def __init__(self):
        self.my_dummy_attribute = "yey!"

    def test_dummy_function(self):
        return "hello"


class TestAutoSpec(unittest.TestCase):

    @patch('test_func.MyDummyClass', autospec=True)
    def test_autospec_function(self, mock_dummy_class):
        self.assertTrue(hasattr(mock_dummy_class, "test_dummy_function"))

    @patch('test_func.MyDummyClass', autospec=True)
    def test_autospec_attribute(self, mock_dummy_class):
        self.assertTrue(hasattr(mock_dummy_class, "test_dummy_attribute"))
```

The snippet above will result to this:

```
python -m unittest test_func.py -v
test_autospec_attribute (test_func.TestAutoSpec) ... FAIL
test_autospec_function (test_func.TestAutoSpec) ... ok
======================================================================
FAIL: test_autospec_attribute (test_func.TestAutoSpec)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 1179, in patched
    return func(*args, **keywargs)
  File "/Users/ryan/Workspace/playground/test_func.py", line 22, in test_autospec_attribute
    self.assertTrue(hasattr(mock_dummy_class, "test_dummy_attribute"))
AssertionError: False is not true

----------------------------------------------------------------------
Ran 2 tests in 0.010s
FAILED (failures=1)
```

The `test_autospec_attribute` failed because the mocked object doesn’t know about the `test_dummy_attribute` which is dynamically created in the `__init__()`.

## Conclusion

Autospeccing is a great tool for testing. It minimize the errors caused by misspelling of the methods and making sure our mocked objects are updated based on the changes of the original object which is a great help for refactoring.

But like any other tools, don't overuse it. Understanding the tools and knowing the applicable use of those tools are utmost important.

Happy testing!

## Resources

- [Python Documentation: Autospeccing](https://docs.python.org/3.5/library/unittest.mock.html#autospeccing)
- [Python Documentation: create_autospec](https://docs.python.org/3.5/library/unittest.mock.html#unittest.mock.create_autospec)
- [When using unittest.mock.patch, why is autospec not True by default?](https://stackoverflow.com/questions/35915703/when-using-unittest-mock-patch-why-is-autospec-not-true-by-default)
- [Enriching Your Python Classes With Dunder (Magic, Special) Methods](https://dbader.org/blog/python-dunder-methods)