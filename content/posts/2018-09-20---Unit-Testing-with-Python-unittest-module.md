---
title: "Unit Testing with Python unittest module"
date: "2018-09-20"
template: "post"
draft: false
slug: "unit-testing-with-python-unittest-module"
category: "Software Engineering"
tags:
  - "Python"
  - "unittest"
description: "A well-designed software is build and designed with quality in mind and it doesn’t easily break with small code changes. We can ensure that our code still works regardless of code changes if we’re doing tests. That’s why doing tests is very important part of building a well-designed application because it ensures quality. Aside from software quality this arcticle will discuss other reasons why writing tests on your software is necessary. Also, this article will have a walk-through on how to write unit tests in Python using unittest module."
---

A well-designed software is build and designed with quality in mind and it doesn’t easily break with small code changes. We can ensure that our code still works regardless of code changes if we’re doing tests. That’s why doing tests is very important part of building a well-designed application because it ensures quality. This statement below by [Jacob Kaplan-Moss](https://jacobian.org/), a [django](https://www.djangoproject.com/) core developer, says it all.

> Code without tests is broken by design. - Jacob Kaplan-Moss

So what is unit test, Unit Testing is a process of testing small pieces or unit of your code, making sure it works as expected in a positive and negative scenarios. Our mindset during unit testing should be on that small piece of code we’re testing and not with the whole application. In Python, there are a lot of libraries you can use to do unit test: [pytest](https://docs.pytest.org/en/latest/), [nose](https://nose.readthedocs.io/en/latest/testing.html), [unittest](https://docs.python.org/3.5/library/unittest.html) among others. In this article we will focus on using unittest with python version 3.5.2.

## Why Write Unit Tests?
- It saves us a lot of time

Doing manual testing takes a lot of time and effort, imagine we need to test every positive and negative scenarios every time we did an update on our codebase, the larger the code base, the larger the effort and time we put on manual testing and there’s a big possibility that we’ll missed a lot of scenarios. By writing unit tests, we can easily automate our tests and rerun it every time we update our code. Running automated tests would take a minute or two compared with manual testing that could take more than 15 minutes. Unit Test formalize the test approach in a way it saves time and effort.

- Increase Code Quality

Developing new features, fixing bugs, or code refactoring might introduce new bugs or breaks our code in any way. Unit test prevent or lessen the chance of that from happening. By running our unit tests every time we did an update in our codebase we can validate if those updates breaks any existing feature(s) in our application. Also, writing tests forces us to think about the non-normal conditions or edges cases our code might encounter and prevent them as soon as possible.

- Helps us to have a Good System Design

As stated above, Unit Testing is a process of testing small pieces or unit of our code. That’s why unit test should be small, if you realized that you’re writing a large amount of code for unit test just for a single function, you need to refactor that function to make it small, modular, and loosely coupled. A modular and loosely coupled application is a well-designed application.

- Unit Tests also serves as a documentation

As a new member of a team, looking at the unit tests of the codebase is a great help for understanding the project. Unit Tests shows what would be the behavior of the functions/modules in a specific scenario. This serves as the business-rule documentation inside our codebase.

## Python unittest module walk-through

We’ll use this simple test script below to demonstrate how to write and run unit tests using python unittest module.

```
# test_foo.py
import unittest

# function to be tested
def add(a, b):
    return a + b

class FooTestCase(unittest.TestCase):    
    
    # test method       
    def test_add(self):
        sum = add(1, 2) 
        
        # validate the result of the code we're testing.
        self.assertEqual(3, sum)    
    
    def test_add_negative_numbers(self):
        sum = add(-1-2)
        self.assertEqual(-3, sum)
```

&nbsp;
### To write a test script using python unittest module:

- Create an empty python file with the filename prefixed by `test_` . This way we can easily distinguish that a file is a test script.
- We start by importing the unittest module, we don't need to install anything as it is part of python standard library.
- We write our testcases. Writing testcases using unittest module follows the object oriented programming paradigm. Our testcase is a class which extends from `unittest.TestCase`. TestCase from unittest module has the methods and attributes we need in running and validating our code.
- We create test methods to validate different scenarios in our code. We use [`assert* methods`](https://docs.python.org/3.5/library/unittest.html#unittest.TestCase.debug) from `unittest.TestCase` to validate our code based on our expected output and the result of the code we’re testing, as with the example above we’re validating if the `add()` function return value is equal with our expected value. When writing test methods its a best practice to make the method name as descriptive as possible, its a good idea to use sentence-like format when naming test methods as this will show in the report after running all our tests. Also don't forget to prefix your test method with `test_` so unittest will know that they will be run, evaluated, and include in test reporting.

### To run test scripts written via unittest module:

```
python -m unittest test_foo -v
```

which will result into this:

```
test_add (test_foo.TestFoo) ... ok
test_add_negative_numbers (test_foo.TestFoo) ... ok

------------------------------------------------------------------
Ran 2 tests in 0.000s
OK
```

The test result is very easy to understand, It shows the total test count and the test methods that is included in our test script. And when we encounter an error or failure in our test, it gives helpful information to fix the issue. for example:

```
test_add (test_foo.TestFoo) ... FAIL
test_add_negative_numbers (test_foo.TestFoo) ... ok

==================================================================
FAIL: test_add (test_foo.TestFoo)
------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/ryan/Workspace/playground/python-unittest/test_foo.py", line 13, in test_add
    self.assertEqual(4, sum)
AssertionError: 4 != 3

------------------------------------------------------------------
Ran 2 tests in 0.000s
FAILED (failures=1)
```

There are many ways to run a unittest script which is flexible enough to meet our needs. I include the `-v` option to have a verbose output.

```
# discover and run all test scripts 
# from current package and its sub package.
python -m unittest discover -v

# equivalent with the command above.
python -m unittest -v

# discover and run all test scripts 
# from specified package and its sub modules.
python -m unittest <package> -v

# run specific test script
python -m test_script -v# run specific test class inside a test script
python -m unittest test_module.TestClass -v

# run specific test method of a test class of a test module
python -m unittest test_module.TestClass.test_method -v
```

Also, we can make our test script executable. By doing so, we can remove `-m unittest` and run the test script as normal python script. But there’s a caveat, we can only run the test script as a whole and not by testcase class or by test methods.

```
if __name__ == '__main__':
    unittest.main()
```

Including the code block above at the bottom of our test script, we can now run our test script using this command.

```
python test_foo.py -v
```

We can skip a test method in our testcase using `@unittest.skip("your reason")`

```
@unittest.skip("skipping test because i like it.")
def test_add_negative_numbers(self):
        sum = add(-1,-2)                                
        self.assertEqual(-3, sum)
```

This will give us this result:

```
test_add (test_foo.TestFoo) ... ok
test_add_negative_numbers (test_foo.TestFoo) ... skipped 'skipping test because i like it.'

------------------------------------------------------------------
Ran 2 tests in 0.000sOK (skipped=1)
```

&nbsp;
### Test setup and cleanup
Most of the time we need to run a code before and after a test method. For example, before running our test method we need to setup the database connection, initialize our classes, prepare our test data, etc. Also after we run our test method, we need to close our database connection and clear any traces of our tests. These actions are called **test fixtures**.

In python unittest module, this test fixtures are possible by overriding these methods in our testcase class:

- `setUp()` — runs before every test method.
- `tearDown()` — runs after every test method.
- `setUpClass()` — runs before all the test methods. This is decorated by `@classmethod`
- `tearDownClass()` — runs after all the test methods. This is decorated by `@classmethod`

```
# test_foo.py
import unittestdef add(a, b):
    return a + bclass TestFoo(unittest.TestCase):    
    
    @classmethod
    def setUpClass(self):
        print("this will run before all test methods.")    
        
    @classmethod    
    def tearDownClass(self):    
        print("this will run after all test methods.")   
 
    def setUp(self):
        print("this will run before every test method.")    
        
    def tearDown(self):    
        print("this will run after every test method.")   
        
    def test_add(self):
        sum = add(1, 2)
        print("I am test_add()")
        self.assertEqual(3, sum)
 
    def test_add_negative_numbers(self):
        sum = add(-1, -2)
        print("I am test_add_negative_numbers()")
        self.assertEqual(-3, sum)
```

The test script above will give us this result:

```
this will run before all test methods.
test_add (test_foo.TestFoo) ... this will run before every test method.
I am test_add()
this will run after every test method.
ok
test_add_negative_numbers (test_foo.TestFoo) ... this will run before every test method.
I am test_add_negative_numbers()
this will run after every test method.
ok
this will run after all test methods.

------------------------------------------------------------------
Ran 2 tests in 0.000s
OK
```

&nbsp;
### Mocking with unittest module

There are times that the function/module which is the subject of our unit test has some dependencies that we don't have any control of, e.g external APIs or services, python standard modules, or other modules that we wrote inside our project. These dependencies might cause undesirable side effects during our test and might hinder the test itself. Good thing we can mock these dependencies and focus only on the code that we need to test.

Let’s use this simple functions below to explore how to mock function/module using unittest module:

```
# test_foo.py# function from third party package/module.
def is_legal_age(age):
    return age >= 18# function to be tested
def poison():
    if is_legal_age(18):
        return "Vodka"
    return "Apple Juice"
```

The test script below shows how we test the `poison()` function. Our `poison()` function is dependent to `is_legal_age()` function which is located from other module.


```
import unittest
from unittest.mock import patchclass TestFoo(unittest.TestCase):    

    @patch("test_foo.is_legal_age")
    def test_poison_for_legal_age(self,
                                  mock_people_is_legal_age):
        mock_people_is_legal_age.return_value = False
        self.assertEqual(poison(), "Apple Juice")    
        
    @patch("test_foo.is_legal_age")
    def test_poison_for_illegal_age(self,
                                    mock_people_is_legal_age):
        mock_people_is_legal_age.return_value = True
        self.assertEqual(poison(), "Vodka")
```

- Import `patch` from `unittest.mock`. We’ll use this module to mock functions from other module.
- The simplest way to use `patch` is as a decorator. We’ll decorate our `test_method` with `patch` and use the function/module we want to mock as the decorator argument in string format. We retrieve, via dot notation, the function/module we want to mock from the module where we use them and not where they originally located.
- We pass the mocked function/module down to our test_method as a method argument, by doing so we can use the mocked function/module inside our test_method. Mocking function/module using unittest will replace our function/module with Mock class which has the methods and attributes we can use for mocking. With the example above, we mock the return value of our mocked function using `mocked_function.return_value`.

With multiple `patch` decorator, order is very important. Incorrect order will raise an undesirable effects. The top most `patch` decorator will be the last mocked function/module to be pass to the test_method. for example:

```
@patch("module.function-nth")
@patch("module.function2")
@patch("module.function1")
def test_method(self, 
                mocked_function1,
                mocked_function2,
                mocked_function-nth):
    # do your test here ...
```

That’s it, with just three steps we’re able to mock a function from other module and continue with our unit test with ease. This is just the tip of the iceberg in regards of mocking in python unittest, I’ll create a separate post for this.

### Qualities of a Good Unit Test

- A unit test method name should be descriptive as much as possible, it can be in a sentence-like format. In this way, unit test function name is much more readable, will make sense, and would look like a business-rule documentation.
- Unit test should be isolated from one another, they shouldn’t rely or affect other unit tests inside the application.
- A unit test validate a small piece of code, it should be small and should focus only on that small piece of code.
- A good unit test should not leave any traces. A unit test should be responsible of cleaning its own test data and connections.

### What’s Next?

After learning the basic usage of python unittest module, we can start adding tests in our projects and make it a habit. With what we’ve learned on this article, we can do better by exploring these topics:

- [Practice Test Driven Development (TDD)](https://en.wikipedia.org/wiki/Test-driven_development)
- [Explore unittest Mock](https://docs.python.org/3.5/library/unittest.mock.html)
- [Setup CI/CD](https://bitbucket.org/product/features/pipelines)
- [Measuring your Code Unit Test Coverage](https://coverage.readthedocs.io/en/coverage-4.5.1a/)
- Explore other Testing Tools: [pytest](https://docs.pytest.org/en/latest/) and [nose](https://nose.readthedocs.io/en/latest/testing.html).

### Resources

- [unittest — Unit testing framework](https://docs.python.org/3.5/library/unittest.html)
- [Testing Your Code](https://docs.python-guide.org/writing/tests/)
- [Improve Your Python: Understanding Unit Testing](https://jeffknupp.com/blog/2013/12/09/improve-your-python-understanding-unit-testing/)
- [Python Tutorial: Unit Testing Your Code with the unittest Module](https://www.youtube.com/watch?v=6tNS--WetLI)
- [Andrew Knight | Testing is Fun in Python!](https://www.youtube.com/watch?v=Sb2tz9Hlbp8&t=849s)