---
title: Automating Tests for Your Code
teaching: 30
exercises: 0
questions:
- "How do you test your code?"
objectives:
- "Learn about pytest."
- "Learn about writing simple tests."
keypoints:
- "Pytest is a library that automates testing."
- "Pytest will run any function that starts with test."
- "Tests can be in the same file as your code, but they are better in a separate file."
- "Break your tests into small bits that only focus on one thing."
---

## Testing 

For this section we are going to write a very simple function which adds two numbers. Save this to a file, say mycode.py:

~~~
def add(a,b):
  """Add two numbers together"""
  return a+b
~~~
{: .language-python}

The funcions takes two variables and returns the sum of them. It is really easy to predict what the output of this function is. We can take a few of these predictions and create a test function like so and add it to the same file, mycode.py:

~~~
def test_add():
    """Test the add() function"""
    assert add(1, 2) == 3
    assert add(1.4, 1.3) == 2.7
    assert add(1, 2.5) == 3.5
~~~
{: .language-python}

Notice that the name of the function starts with "test". This tips Python off that this is a function that countains tests. Conversely, it is not recommended that you use "test" in the names of your functions if they are not tests. The function takes no inputs. The body of the function consists of three assert statements.

Now that we have a test, we can run pytest against our function. In the Terminal:

~~~
> pytest mycode.py
~~~
{: .language-python}

~~~
=================================================== test session starts ===================================================
platform darwin -- Python 3.7.2, pytest-4.2.1, py-1.7.0, pluggy-0.8.1
rootdir: /Users/imomcheva/WORK/INTRO, inifile:
plugins: remotedata-0.3.1, openfiles-0.3.2, doctestplus-0.2.0, arraydiff-0.3
collected 1 item

mycode.py .                                                                                                            [100%]

================================================ 1 passed in 0.06 seconds =================================================
~~~
{: .output}

This means that one test was found and successfully run.

Now, we see that our function works on integers and floats, but what does it do if you pass it two strings? What should it do? We can find out what it does by adding yet another test to our mycode.py file, this time with two strings:

~~~
def test_add_interface():
  assert add('foo','bar') == 0
~~~
{: .language-python}

Here we assert that the sum of two strings should be zero, which is likely to be wrong. Let's run the test again:

~~~
> pytest mycode.py
~~~
{: .language-python}

~~~
=================================================== test session starts ===================================================
platform darwin -- Python 3.7.2, pytest-4.2.1, py-1.7.0, pluggy-0.8.1
rootdir: /Users/imomcheva/WORK/INTRO, inifile:
plugins: remotedata-0.3.1, openfiles-0.3.2, doctestplus-0.2.0, arraydiff-0.3
collected 2 items

tmp.py .F                                                                                                           [100%]

======================================================== FAILURES =========================================================
___________________________________________________ test_add_interface ____________________________________________________

    def test_add_interface():
>     assert add('foo','bar') == 0
E     AssertionError: assert 'foobar' == 0
E      +  where 'foobar' = add('foo', 'bar')

tmp.py:12: AssertionError
=========================================== 1 failed, 1 passed in 0.14 seconds ============================================
~~~
{: .output}

Now we get a failed test because our add function returns a string 'foobar' which we asserted should be zero. How should we deal with string inputs? One way is to insist that our function will only handle integers and strings and return a TypeError (which we saw earlier) for all other inputs. How do we actually implement this?

~~~
def add(a,b):
  if isinstance(a, (int,float)) and isinstance(b, (int,float)):
    return a+b
  else:
    raise TypeError('a and b should be numbers')
~~~
{: .language-python}

Here we first check if a and b are instances of the integer or float class using the isinstance function. If not, we raise a TypeError and provide a custom message for the user. We could have just printed a message but by using a standard error types, we can take advantage of Python's internal way of dealing with errors. For example, we can now restructure our test function in a way that handles this error: 

~~~
import pytest
def test_add_interface():
  with pytest.raises(TypeError)
    add('foo','bar')
~~~
{: .language-python}

Try running pytest against this new version and see if all tests pass. 

Finally let's see how our function handles NaNs. If you add a number to a NaN, you might expect to also get a NaN. So a simple way to test for this:

~~~
def test_add_nan():
  assert add(2, np.nan) == np.nan
~~~
{: .language-python}

Test it? Python does not like to compare one NaN to another NaN. So instead we need to use the numpy.isnan() funciton to check if our result is NaN:

~~~
import numpy as np
def test_add_nan():
  results = add(2, np.nan)
  assert np.isnan(result)
~~~
{: .language-python}  

## Separating Tests and Code

As we have added tests, we have also added dependencies to our little function. And if our code gets longer we may start mixing the functional code and tests. Let us separate the tests into their own separate file, test_add.py,  with the dependencies on top:

~~~
import pytest
import numpy as np
from mycode import add


def test_add():
    """Test the add() function"""
    assert add(1, 2) == 3
    assert add(1.4, 1.3) == 2.7
    assert add(1, 2.5) == 3.5


def test_add_interface():
    """Test that strings don't work"""
    with pytest.raises(TypeError):
        add("foo", "bar")


def test_add_nan():
    result = add(1, np.nan)
    assert np.isnan(result)
~~~
{: .language-python}

Then our actual function file, mycode.py, is only left with the following content.

~~~
def add(a, b):
    """Add two numbers together"""
    if isinstance(a, (int, float)) and isinstance(b, (int, float)):
        return a + b
    else:
        raise TypeError("a and b should be numbers")
~~~
{: .language-python}

To run your test suite in the Terminal:

~~~
> pytest
~~~
{: .language-python}

~~~
=================================================== test session starts ===================================================
platform darwin -- Python 3.7.2, pytest-4.2.1, py-1.7.0, pluggy-0.8.1
rootdir: /Users/imomcheva/WORK/INTRO, inifile:
plugins: remotedata-0.3.1, openfiles-0.3.2, doctestplus-0.2.0, arraydiff-0.3
collected 3 items

test_mycode.py ...                                                                                                  [100%]

================================================ 3 passed in 0.19 seconds =================================================
~~~
{: .output}

The same can be done from within the ipython interpreter:

~~~
import pytest
pytest.main()
~~~
{: .language-python}

~~~
=================================================== test session starts ===================================================
platform darwin -- Python 3.7.2, pytest-4.2.1, py-1.7.0, pluggy-0.8.1
rootdir: /Users/imomcheva/WORK/INTRO, inifile:
plugins: remotedata-0.3.1, openfiles-0.3.2, doctestplus-0.2.0, arraydiff-0.3
collected 3 items

test_mycode.py ...                                                                                                  [100%]

================================================ 3 passed in 0.17 seconds =================================================
~~~
{: .output}


{% include links.md %}
