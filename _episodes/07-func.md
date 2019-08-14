---
title: Creating Functions
teaching: 30
exercises: 0
questions:
- "How can I define new functions?"
- "What's the difference between defining and calling a function?"
- "What happens when I call a function?"
objectives:
- "Define a function that takes parameters."
- "Return a value from a function."
- "Test and debug a function."
- "Set default values for function parameters."
- "Explain why we should divide programs into small, single-purpose functions."
keypoints:
- "Define a function using `def function_name(parameter)`."
- "The body of a function must be indented."
- "Call a function using `function_name(value)`."
- "Numbers are stored as integers or floating-point numbers."
- "Variables defined within a function can only be seen and used within the body of the function."
- "If a variable is not defined within the function it is used,
   Python looks for a definition before the function call"
- "Use `help(thing)` to view help for something."
- "Put docstrings in functions to provide help for that function."
- "Specify default values for parameters when defining a function using `name=value`
   in the parameter list."
- "Parameters can be passed by matching based on name, by position,
   or by omitting them (in which case the default value is used)."
- "Put code whose parameters change frequently in a function,
   then call it with different parameter values to customize its behavior."
---

At this point,
we've written code to draw some interesting features in our supernova data,
loop over all our data files to quickly draw these plots for each of them,
and have Python make decisions based on what it sees in our data.
But, our code is getting pretty long and complicated;
what if we had thousands of datasets,
and didn't want to generate a figure for every single one?
Commenting out the figure-drawing code is a nuisance.
Also, what if we want to use that code again,
on a different dataset or at a different point in our program?
Cutting and pasting it is going to make our code get very long and very repetitive,
very quickly.
We'd like a way to package our code so that it is easier to reuse,
and Python provides for this by letting us define things called 'functions' ---
a shorthand way of re-executing longer pieces of code.
Let's start by defining a function `fahr_to_celsius` that converts temperatures
from Fahrenheit to Celsius:

~~~
def fahr_to_celsius(temp):
    return ((temp - 32) * (5/9))
~~~
{: .language-python}

![The Blueprint for a Python Function](../fig/python-function.svg)


The function definition opens with the keyword `def` followed by the
name of the function (`fahr_to_celsius`) and a parenthesized list of parameter names (`temp`). The
[body]({{ page.root }}/reference/#function-body) of the function --- the
statements that are executed when it runs --- is indented below the
definition line.  The body concludes with a `return` keyword followed by the return value.

When we call the function,
the values we pass to it are assigned to those variables
so that we can use them inside the function.
Inside the function,
we use a [return statement]({{ page.root }}/reference/#return-statement) to send a result
back to whoever asked for it.

Let's try running our function.

~~~
fahr_to_celsius(32)
~~~
{: .language-python}

This command should call our function, using "32" as the input and return the function value.

In fact, calling our own function is no different from calling any other function:
~~~
print('freezing point of water:', fahr_to_celsius(32), 'C')
print('boiling point of water:', fahr_to_celsius(212), 'C')
~~~
{: .language-python}

~~~
freezing point of water: 0.0 C
boiling point of water: 100.0 C
~~~
{: .output}

We've successfully called the function that we defined,
and we have access to the value that we returned.


## Composing Functions

Now that we've seen how to turn Fahrenheit into Celsius,
we can also write the function to turn Celsius into Kelvin:

~~~
def celsius_to_kelvin(temp_c):
    return temp_c + 273.15

print('freezing point of water in Kelvin:', celsius_to_kelvin(0.))
~~~
{: .language-python}

~~~
freezing point of water in Kelvin: 273.15
~~~
{: .output}

What about converting Fahrenheit to Kelvin?
We could write out the formula,
but we don't need to.
Instead,
we can [compose]({{ page.root }}/reference/#compose) the two functions we have already created:

~~~
def fahr_to_kelvin(temp_f):
    temp_c = fahr_to_celsius(temp_f)
    temp_k = celsius_to_kelvin(temp_c)
    return temp_k

print('boiling point of water in Kelvin:', fahr_to_kelvin(212.0))
~~~
{: .language-python}

~~~
boiling point of water in Kelvin: 373.15
~~~
{: .output}

This is our first taste of how larger programs are built:
we define basic operations,
then combine them in ever-large chunks to get the effect we want.
Real-life functions will usually be larger than the ones shown here --- typically half a dozen
to a few dozen lines --- but they shouldn't ever be much longer than that,
or the next person who reads it won't be able to understand what's going on.

## Tidying up

Now that we know how to wrap bits of code up in functions,
we can make our supernova analysis easier to read and easier to reuse.

Let's make a function called `detect_problems` that checks for those systematics
we noticed:

~~~

def detect_problems(filename):

    data = np.loadtxt(fname=filename, delimiter=',', skiprows=1)

    for i in [1,3,5,7]:
        if np.nanmin(data[:,i]) < 0.:
            print(filename + ': A negative flux.')
        elif np.sum(np.isnan(data[:,i])) == data.shape[0]:
            print(filename + ': A NaN column')
        else:
            print('Seems OK!')
~~~
{: .language-python}

Notice that rather than jumbling this code together in one giant `for` loop,
we can now read and reuse both ideas separately.
We can reproduce the previous analysis with a much simpler `for` loop:

~~~
import glob
import numpy as np
filenames = sorted(glob.glob('data/*.csv'))

for f in filenames[:3]:
    print(f)
    detect_problems(f)
~~~
{: .language-python}

By giving our functions human-readable names,
we can more easily read and understand what is happening in the `for` loop.
Even better, if at some later date we want to use either of those pieces of code again,
we can do so in a single line.

## Documenting

Once we start putting things in functions so that we can re-use them,
it is useful to add some documentation so that we remember what the function does.

The usual way to put documentation in software is
to add [comments]({{ page.root }}/reference/#comment) like this:

~~~
#fahr_to_celsius(temp):
#converts fahrenheit degrees to celsius degrees
def fahr_to_celsius(temp):
    return ((temp - 32) * (5/9))
~~~
{: .language-python}

There's a better way, though.
If the first thing in a function is a string that isn't assigned to a variable,
that string is attached to the function as its documentation:

~~~
def fahr_to_celsius(temp):
    """Converts fahrenheit degrees to celsius degrees."""
    return ((temp - 32) * (5/9))
~~~
{: .language-python}

This is better because we can now ask Python's built-in help system to show us
the documentation for the function:

~~~
help(fahr_to_celsius)
~~~
{: .language-python}

~~~
Help on function fahr_to_celsius in module __main__:

fahr_to_celsius(temp)
   Converts fahrenheit degrees to celsius degrees.
~~~
{: .output}

A string like this is called a [docstring]({{ page.root }}/reference/#docstring).
We don't need to use triple quotes when we write one,
but if we do,
we can break the string across multiple lines.

~~~
def fahr_to_celsius(temp):
    """
    Converts fahrenheit degrees to celsius degrees.
    Example:
    >fahr_to_celsius(125)
    37.77777777777778
    """
    return ((temp - 32) * (5/9))

help(fahr_to_celsius)
~~~
{: .language-python}

~~~
Help on function fahr_to_celsius in module __main__:

fahr_to_celsius(temp)
    Converts fahrenheit degrees to celsius degrees.
    Example:
    >fahr_to_celsius(125)
    37.77777777777778
~~~
{: .output}

## Defining Defaults

We have passed parameters to functions in two ways:
directly, as in `type(data)`,
and by name, as in `numpy.loadtxt(fname='something.csv', delimiter=',')`.
In fact,
we can pass the filename to `loadtxt` without the `fname=`:

~~~
np.loadtxt('data/03D1ar.csv', delimiter=',',skiprows=1)
~~~
{: .language-python}

~~~
array([[ 0.,  0.,  1., ...,  3.,  0.,  0.],
       [ 0.,  1.,  2., ...,  1.,  0.,  1.],
       [ 0.,  1.,  1., ...,  2.,  1.,  1.],
       ...,
       [ 0.,  1.,  1., ...,  1.,  1.,  1.],
       [ 0.,  0.,  0., ...,  0.,  2.,  0.],
       [ 0.,  0.,  1., ...,  1.,  1.,  0.]])
~~~
{: .output}

but we still need to say `delimiter=`:

~~~
np.loadtxt('data/03D1ar.csv', ',',1)
~~~
{: .language-python}

~~~
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/username/anaconda3/lib/python3.6/site-packages/numpy/lib/npyio.py", line 1041, in loa
dtxt
    dtype = np.dtype(dtype)
  File "/Users/username/anaconda3/lib/python3.6/site-packages/numpy/core/_internal.py", line 199, in
_commastring
    newitem = (dtype, eval(repeats))
  File "<string>", line 1
    ,
    ^
SyntaxError: unexpected EOF while parsing
~~~
{: .error}

With that in hand,
let's look at the help for `np.loadtxt`:

~~~
help(np.loadtxt)
~~~
{: .language-python}

~~~
Help on function loadtxt in module np.lib.npyio:

loadtxt(fname, dtype=<class 'float'>, comments='#', delimiter=None, converters=None, skiprows=0, use
cols=None, unpack=False, ndmin=0, encoding='bytes')
    Load data from a text file.

    Each row in the text file must have the same number of values.

    Parameters
    ----------
...
~~~
{: .output}

There's a lot of information here,
but the most important part is the first couple of lines:

~~~
loadtxt(fname, dtype=<class 'float'>, comments='#', delimiter=None, converters=None, skiprows=0, use
cols=None, unpack=False, ndmin=0, encoding='bytes')
~~~
{: .output}

This tells us that `loadtxt` has one parameter called `fname` that doesn't have a default value,
and eight others that do.
If we call the function like this:

~~~
np.loadtxt('data/03D1ar.csv', ',',1)
~~~
{: .language-python}

then the filename is assigned to `fname` (which is what we want),
but the delimiter string `','` is assigned to `dtype` rather than `delimiter`,
because `dtype` is the second parameter in the list. However `','` isn't a known `dtype` so
our code produced an error message when we tried to run it.
When we call `loadtxt` we don't have to provide `fname=` for the filename because it's the
first item in the list, but if we want the `','` to be assigned to the variable `delimiter`,
we *do* have to provide `delimiter=` for the second parameter since `delimiter` is not
the second parameter in the list.

## Readable functions

Consider these two functions:

~~~
def s(p):
    a = 0
    for v in p:
        a += v
    m = a / len(p)
    d = 0
    for v in p:
        d += (v - m) * (v - m)
    return np.sqrt(d / (len(p) - 1))

def std_dev(sample):
    sample_sum = 0
    for value in sample:
        sample_sum += value

    sample_mean = sample_sum / len(sample)

    sum_squared_devs = 0
    for value in sample:
        sum_squared_devs += (value - sample_mean) * (value - sample_mean)

    return np.sqrt(sum_squared_devs / (len(sample) - 1))
~~~
{: .language-python}

The functions `s` and `std_dev` are computationally equivalent (they
both calculate the sample standard deviation), but to a human reader,
they look very different. You probably found `std_dev` much easier to
read and understand than `s`.

As this example illustrates, both documentation and a programmer's
_coding style_ combine to determine how easy it is for others to read
and understand the programmer's code. Choosing meaningful variable
names and using blank spaces to break the code into logical "chunks"
are helpful techniques for producing _readable code_. This is useful
not only for sharing code with others, but also for the original
programmer. If you need to revisit code that you wrote months ago and
haven't thought about since then, you will appreciate the value of
readable code!

> ## Combining Strings
>
> "Adding" two strings produces their concatenation:
> `'a' + 'b'` is `'ab'`.
> Write a function called `fence` that takes two parameters called `original` and `wrapper`
> and returns a new string that has the wrapper character at the beginning and end of the original.
> A call to your function should look like this:
>
> ~~~
> print(fence('name', '*'))
> ~~~
> {: .language-python}
>
> ~~~
> *name*
> ~~~
> {: .output}
>
> > ## Solution
> > ~~~
> > def fence(original, wrapper):
> >     return wrapper + original + wrapper
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Return versus print
>
> Note that `return` and `print` are not interchangeable.
> `print` is a Python function that *prints* data to the screen.
> It enables us, *users*, see the data.
> `return` statement, on the other hand, makes data visible to the program.
> Let's have a look at the following function:
>
> ~~~
> def add(a, b):
>     print(a + b)
> ~~~
> {: .language-python}
>
> **Question**: What will we see if we execute the following commands?
> ~~~
> A = add(7, 3)
> print(A)
> ~~~
> {: .language-python}
>
> > ## Solution
> > Python will first execute the function `add` with `a = 7` and `b = 3`,
> > and, therefore, print `10`. However, because function `add` does not have a
> > line that starts with `return` (no `return` "statement"), it will, by default, return
> > nothing which, in Python world, is called `None`. Therefore, `A` will be assigned to `None`
> > and the last line (`print(A)`) will print `None`. As a result, we will see:
> > ~~~
> > 10
> > None
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

> ## Selecting Characters From Strings
>
> If the variable `s` refers to a string,
> then `s[0]` is the string's first character
> and `s[-1]` is its last.
> Write a function called `outer`
> that returns a string made up of just the first and last characters of its input.
> A call to your function should look like this:
>
> ~~~
> print(outer('helium'))
> ~~~
> {: .language-python}
>
> ~~~
> hm
> ~~~
> {: .output}
>
> > ## Solution
> > ~~~
> > def outer(input_string):
> >     return input_string[0] + input_string[-1]
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Rescaling an Array
>
> Write a function `rescale` that takes an array as input
> and returns a corresponding array of values scaled to lie in the range 0.0 to 1.0.
> (Hint: If `L` and `H` are the lowest and highest values in the original array,
> then the replacement for a value `v` should be `(v-L) / (H-L)`.)
>
> > ## Solution
> > ~~~
> > def rescale(input_array):
> >     L = np.min(input_array)
> >     H = np.max(input_array)
> >     output_array = (input_array - L) / (H - L)
> >     return output_array
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Testing and Documenting Your Function
>
> Run the commands `help(np.arange)` and `help(np.linspace)`
> to see how to use these functions to generate regularly-spaced values,
> then use those values to test your `rescale` function.
> Once you've successfully tested your function,
> add a docstring that explains what it does.
>
> > ## Solution
> > ~~~
> > '''Takes an array as input, and returns a corresponding array scaled so
> > that 0 corresponds to the minimum and 1 to the maximum value of the input array.
> >
> > Examples:
> > >>> rescale(np.arange(10.0))
> > array([ 0.        ,  0.11111111,  0.22222222,  0.33333333,  0.44444444,
> >        0.55555556,  0.66666667,  0.77777778,  0.88888889,  1.        ])
> > >>> rescale(np.linspace(0, 100, 5))
> > array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])
> > '''
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Defining Defaults
>
> Rewrite the `rescale` function so that it scales data to lie between `0.0` and `1.0` by default,
> but will allow the caller to specify lower and upper bounds if they want.
> Compare your implementation to your neighbor's:
> do the two functions always behave the same way?
>
> > ## Solution
> > ~~~
> > def rescale(input_array, low_val=0.0, high_val=1.0):
> >     '''rescales input array values to lie between low_val and high_val'''
> >     L = np.min(input_array)
> >     H = np.max(input_array)
> >     intermed_array = (input_array - L) / (H - L)
> >     output_array = intermed_array * (high_val - low_val) + low_val
> >     return output_array
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Variables Inside and Outside Functions
>
> What does the following piece of code display when run --- and why?
>
> ~~~
> f = 0
> k = 0
>
> def f2k(f):
>     k = ((f-32)*(5.0/9.0)) + 273.15
>     return k
>
> f2k(8)
> f2k(41)
> f2k(32)
>
> print(k)
> ~~~
> {: .language-python}
>
> > ## Solution
> >
> > ~~~
> > 259.81666666666666
> > 287.15
> > 273.15
> > 0
> > ~~~
> > {: .output}
> > `k` is 0 because the `k` inside the function `f2k` doesn't know
> > about the `k` defined outside the function.
> {: .solution}
{: .challenge}

> ## Mixing Default and Non-Default Parameters
>
> Given the following code:
>
> ~~~
> def numbers(one, two=2, three, four=4):
>     n = str(one) + str(two) + str(three) + str(four)
>     return n
>
> print(numbers(1, three=3))
> ~~~
> {: .language-python}
>
> what do you expect will be printed?  What is actually printed?
> What rule do you think Python is following?
>
> 1.  `1234`
> 2.  `one2three4`
> 3.  `1239`
> 4.  `SyntaxError`
>
> Given that, what does the following piece of code display when run?
>
> ~~~
> def func(a, b=3, c=6):
>     print('a: ', a, 'b: ', b, 'c:', c)
>
> func(-1, 2)
> ~~~
> {: .language-python}
>
> 1. `a: b: 3 c: 6`
> 2. `a: -1 b: 3 c: 6`
> 3. `a: -1 b: 2 c: 6`
> 4. `a: b: -1 c: 2`
>
> > ## Solution
> > Attempting to define the `numbers` function results in `4. SyntaxError`.
> > The defined parameters `two` and `four` are given default values. Because
> > `one` and `three` are not given default values, they are required to be
> > included as arguments when the function is called and must be placed
> > before any parameters that have default values in the function definition.
> >
> > The given call to `func` displays `a: -1 b: 2 c: 6`. -1 is assigned to
> > the first parameter `a`, 2 is assigned to the next parameter `b`, and `c` is
> > not passed a value, so it uses its default value 6.
> {: .solution}
{: .challenge}

> ## The Old Switcheroo
>
> Consider this code:
>
> ~~~
> a = 3
> b = 7
>
> def swap(a, b):
>     temp = a
>     a = b
>     b = temp
>
> swap(a, b)
>
> print(a, b)
> ~~~
> {: .language-python}
>
> Which of the following would be printed if you were to run this code?
> Why did you pick this answer?
>
> 1. `7 3`
> 2. `3 7`
> 3. `3 3`
> 4. `7 7`
>
> > ## Solution
> > `3 7` is the correct answer. Initially, `a` has a value of 3 and `b` has a value of 7.
> > When the `swap` function is called, it creates local variables (also called
> > `a` and `b` in this case) and trades their values. The function does not
> > return any values and does not alter `a` or `b` outside of its local copy.
> > Therefore the original values of `a` and `b` remain unchanged.
> {: .solution}
{: .challenge}

> ## Readable Code
>
> Revise a function you wrote for one of the previous exercises to try to make
> the code more readable. Then, collaborate with one of your neighbors
> to critique each other's functions and discuss how your function implementations
> could be further improved to make them more readable.
{: .challenge}

{% include links.md %}
