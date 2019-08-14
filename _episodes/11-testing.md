---
title: Automating Tests for Your Code
teaching: 30
exercises: 0
questions:
- "What is you quest?"
- ""
objectives:
- "Staying alive"
- ""
keypoints:
- "Foofofoo."
- ""
---

def add(a,b):
  return a+b
  
def test_add():
  assert add(1,2) == 3
  assert add(1.4, 2.4) == 3.8

def test_add_interface():
  assert add('foo','bar') == 0

> pytest mycode.py

def add(a,b):
  if isinstance(a, (int,float)) and isinstance(b, (int,float)):
    return a+b
  else:
    raise TypeError('a and b should be numbers')

def test_add_interface():
  with pytest.raises(TypeError)
    add('foo','bar')

and import pytest at the top

def test_add_nan():
  assert add(2, np.nan) == np.nan
  actually this doesn't work, needs to be 
  results = add(2, np.nan)
  assert np.isnan(result)
  

Put the test files in a separate file in the same directory in tests_add.py
at the top:
import pytest
import numpy as np
from .mycode.py import add

>pytest


{% include links.md %}
