---
title: Analyzing Data from Multiple Files
teaching: 20
exercises: 0
questions:
- "How can I do the same operations on many different files?"
objectives:
- "Use a library function to get a list of filenames that match a wildcard pattern."
- "Write a `for` loop to process multiple files."
keypoints:
- "Use `glob.glob(pattern)` to create a list of files whose names match a pattern."
- "Use `*` in a pattern to match zero or more characters, and `?` to match any single character."
---

We now have almost everything we need to process all our data files.
The only thing that's missing is a library with a rather unpleasant name:

~~~
import glob
~~~
{: .language-python}

The `glob` library contains a function, also called `glob`,
that finds files and directories whose names match a pattern.
We provide those patterns as strings:
the character `*` matches zero or more characters,
while `?` matches any one character.
We can use this to get the names of all the CSV files in the current directory:

~~~
print(glob.glob('data/*.csv'))
~~~
{: .language-python}

~~~
['data/03D1a1.csv', 'data/03D1ar.csv', 'data/03D1au.csv', 'data/03D1aw.csv', 'data/03D1ax.csv', 'data/03D1bk.csv', 'data/03D1bm.csv', 'data/03D1bp.csv', 'data/03D1cm.csv', 'data/03D1co.csv', 'data/03D1dj.csv', 'data/03D1dt.csv', 'data/03D1ew.csv', 'data/03D1fc.csv', 'data/03D1fq.csv', 'data/03D1gt.csv', 'data/03D3af.csv',...
'data/06D4cl.csv', 'data/06D4co.csv', 'data/06D4cq.csv', 'data/06D4dh.csv']
~~~
{: .output}

As these examples show,
`glob.glob`'s result is a list of file and directory paths in arbitrary order.
This means we can loop over it
to do something with each filename in turn.
In our case,
the "something" we want to do is deternine the minimum and maximum brightness for each supernova dataset.
If we want to start by analyzing just the first three files in alphabetical order, we can use the
`sorted` built-in function to generate a new sorted list from the `glob.glob` output:

~~~
import numpy
import glob

filenames = sorted(glob.glob('data/*.csv'))
filenames = filenames[0:3]
for f in filenames:
    print(f)

    data = numpy.loadtxt(fname=f, delimiter=',', skiprows=1)

    y_min = numpy.nanmin(data[:,[1,3,5,7]])
    y_max = numpy.nanmax(data[:,[1,3,5,7]])
    
    print(f, 'Min brightness: ', y_min, 'Max brightness: ', y_max)

~~~
{: .language-python}

~~~
data/03D1ar.csv
data/03D1ar.csv Min brightness:  -117.01 Max brightness:  694.11
data/03D1au.csv
data/03D1au.csv Min brightness:  -4.7982 Max brightness:  1510.4
data/03D1aw.csv
data/03D1aw.csv Min brightness:  -16.833 Max brightness:  956.81
~~~
{: .output}

You could also use this loop to plot each figure :

~~~
import matplotlib.pyplot

filenames = sorted(glob.glob('data/*.csv'))
filenames = filenames[0:3]
for f in filenames:

    print('Plotting {}...'.format(f))

    data = numpy.loadtxt(fname=f, delimiter=',', skiprows=1)
    col1 = data[:, 1]
    col3 = data[:, 3]

    plt.scatter(col1, col2, color='blue', alpha=.5)
    plt.xlabel('Column 1')
    plt.ylabel('Column 2')
    plt.title('Col 1 vs 2 for {}'.format(f))
    plt.show()
    plt.clf()
~~~



> ## Plotting Differences (DO NOT USE)
>
> Plot the difference between the average of the first dataset
> and the average of the second dataset,
> i.e., the difference between the leftmost plot of the first two figures.
>
> > ## Solution
> > ~~~
> > import glob
> > import numpy
> > import matplotlib.pyplot
> >
> > filenames = sorted(glob.glob('inflammation*.csv'))
> >
> > data0 = numpy.loadtxt(fname=filenames[0], delimiter=',')
> > data1 = numpy.loadtxt(fname=filenames[1], delimiter=',')
> >
> > fig = matplotlib.pyplot.figure(figsize=(10.0, 3.0))
> >
> > matplotlib.pyplot.ylabel('Difference in average')
> > matplotlib.pyplot.plot(numpy.mean(data0, axis=0) - numpy.mean(data1, axis=0))
> >
> > fig.tight_layout()
> > matplotlib.pyplot.show()
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Generate Composite Statistics (DO NOT USE)
>
> Use each of the files once to generate a dataset containing values averaged over all patients:
>
> ~~~
> filenames = glob.glob('inflammation*.csv')
> composite_data = numpy.zeros((60,40))
> for f in filenames:
>     # sum each new file's data into composite_data as it's read
>     #
> # and then divide the composite_data by number of samples
> composite_data /= len(filenames)
> ~~~
> {: .language-python}
>
> Then use pyplot to generate average, max, and min for all patients.
>
> > ## Solution
> > ~~~
> > import glob
> > import numpy
> > import matplotlib.pyplot
> >
> > filenames = glob.glob('inflammation*.csv')
> > composite_data = numpy.zeros((60,40))
> >
> > for f in filenames:
> >     data = numpy.loadtxt(fname = f, delimiter=',')
> >     composite_data += data
> >
> > composite_data/=len(filenames)
> >
> > fig = matplotlib.pyplot.figure(figsize=(10.0, 3.0))
> >
> > axes1 = fig.add_subplot(1, 3, 1)
> > axes2 = fig.add_subplot(1, 3, 2)
> > axes3 = fig.add_subplot(1, 3, 3)
> >
> > axes1.set_ylabel('average')
> > axes1.plot(numpy.mean(composite_data, axis=0))
> >
> > axes2.set_ylabel('max')
> > axes2.plot(numpy.max(composite_data, axis=0))
> >
> > axes3.set_ylabel('min')
> > axes3.plot(numpy.min(composite_data, axis=0))
> >
> > fig.tight_layout()
> >
> > matplotlib.pyplot.show()
> > ~~~
> > {: .language-python}
>{: .solution}
{: .challenge}

{% include links.md %}
