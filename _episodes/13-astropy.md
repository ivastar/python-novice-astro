---
title: Astropy and Jupyter Notebooks
teaching: 30
exercises: 0
questions:
- "What is Astropy?"
objectives:
- "Learn some basic functionality in Astropy"
- "Learn some basic functionality in Jupyter Notebooks."
keypoints:
- "To start up Jupyter Notebooks type jupyter notebook in your terminal."
- "Know how to execute cells and navigate around the notebook."
- "Understand the pitfalls of the non-linear execution of notebooks."
- "Astropy has several basic sub-modules."
- "A fits file can be read with astropy.io.fits."
- "An image is just a numpy array that can be plotted with matplotlib.pyplot.imshow()."
---

In this episode we will cover the Astropy library. But first we will shift from the Terminal and text editor to Jupyter Notebooks to give you a taste of this coding environment and its functionalities.

## Jupyter Notebook

In the Terminal, with your Astroconda environment loaded, type the following:

~~~
jupyter notebook
~~~
{: .language-python}

This command will open up a window in your browser. 

In the top right corner of this window, use the New button to create a notebook. If more than one options are available in the dropdown menu, do select Python 3.

Do a little walk through the notebook here, types of cells, running cells, adding new ones, non-linear execution.

Imports work just the same:

~~~
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
~~~
{: .language-python}

And so do shell commands:

~~~
ls data/*fits
~~~
{: .language-python}

~~~
data/J_A+A_523_A7_table10.fits      data/J_A+A_523_A7_table9.dat.fits
data/J_A+A_523_A7_table11.dat.fits  data/ib3747060_drz.fits*
~~~
{: .output}

## Astropy

(Astropy)[https://www.astropy.org/]

The astropy librarby has very limited functionality, with more stuff available in the affiliated libraries. 

Reading in a table:

~~~
from astropy.table import Table
tab = Table.read('data/J_A+A_523_A7_table10.fits')
~~~
{: .language-python}

Opening a fits image:

~~~
from astropy.io import fits
image = fits.open('data/ib3747060_drz.fits')
~~~
{: .language-python}

Displaying header:

~~~
print(data[0].header)
~~~
{: .language-python}


Displaying image:
~~~
plt.imshow(data[1], vmin=0.0, vmax=0.1)
~~~
{: .language-python}
