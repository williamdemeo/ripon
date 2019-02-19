## Functional Programming in Python

[William DeMeo](mailto:williamdemeo@gmail.com)  
University of Colorado, Boulder

Teaching at Ripon College  
19 Feb 2019

[williamdemeo@gmail.com](mailto:williamdemeo@gmail.com)

---

### What is functional programming?

+ FP is a programming paradigm or  
  "style of programming"

+ In comparison to *imperative* programs, functional programs tend to be more
  1. reliable
  2. scalable
  3. parallelizable

---

### What gives FP these nice features?

+ Immutability ~~> referential transparency  
  &nbsp;&nbsp;  ~~> data sharing ~~> sound logic

+ Often we **prove** that a functional program is correct!

---

### Functional Programming in Python

+ Python is not a purely functional programming language.

+ Does that mean we must program *disfunctionally* in Python?

  No!

---

### FP in Python

Python lacks some purely functional features, e.g.,

+ unlimited recursion
+ lazy evaluation of all expressions
+ an optimizing compiler

---

### FP in Python

However, we *do* have

+ functions as first-class objects
+ higher-order functions like `filter()`, `map()`, `reduce()`, `sorted()`, `min()`, `max()`

---

Let's explore a few nice ideas from functional programming languages and use them to create succinct, general Python programs.

---

### Imperative vs. functional paradigms: key distinction

+ An **imperative program** works by transforming program state.

  **Ex.** increment the value of `x`

  ```python
  x = 2
  x = x+1  # now x = 3
  ```

+ A **functional program** works by simply calling and composing functions.

  **Ex.** produce a new variable `y` whose value is `x+1`

  ```python
  x = 2
  def addone(x):
    return x+1
  y = addone(x)  # now y = 3 and still x = 2
  ```

---

### FP

Each function evaluation creates a **new object** from existing objects.

Function evaluation more closely parallels mathematical formalisms.

Because of this, we can often use simple algebra to design an algorithm, which clearly handles the edge cases
and boundary conditions.

This makes us more confident that the functions work.

It also makes it easy to locate test cases for formal unit testing.

---

### Imperative Example: sum of squares

```python
def sum_of_squares(xs):
  s = 0
  for x in xs:
    s = s + x**2
  return s
```

---

### Functional Example: sums of squares and cubes

```python
def sum_of_squares(xs):
  if len(xs) == 0: return 0
  return xs[0]**2 + sum_of_squares(xs[1:])
```

```python
def sum_of_cubes(xs):
  if len(xs) == 0: return 0
  return xs[0]**3 + sum_of_cubes(xs[1:])
```

---

### The DRY Principle

```python
def sq(x): return x**2

def cube(x): return x**3

def sum_of_squares(xs):
  if len(xs) == 0: return 0
  return sq(xs[0]) + sum_of_squares(xs[1:])
```

```python
def sum_of_cubes(xs):
  if len(xs) == 0: return 0
  return cube(xs[0]) + sum_of_cubes(xs[1:])
```

**Even worse?**

---

### Functions are first class objects!

```python
def sq(x):
  return x**2

def sum_reduce(xs, f):                # f : X -> X is a function
  if len(xs) == 0: return 0
  return f(xs[0]) + sum_reduce(xs[1:])
```

Now we can use **one** function `sum_reduce` to compute sum of squares and sum of cubes, respectively.

```python
  sum_reduce(xs, sq)

  sum_reduce(xs, lambda x: x**3)     # anonymous functions!
```

---

### Can we do better? 

Can we generalize this further?

What if we change our mind and want **products** of squares and cubes?

```python
def prod_reduce(xs, f):
  if len(xs) == 0: return 0
  return f(xs[0]) * prod_reduce(xs[1:])
```

`prod_reduce` and `sum_reduce` differ by just one character! 

**WET!** How can we fix this?

---

## WET --> DRY

```python
# xs: sequence of elements of type T
# f:  function of type T => T
# b:  function of type T x T => T
# example: b(x, y) = x + y

def dry_reduce(xs, f, b):
  if len(xs) == 0: return 0
  return b( xs[0], dry_reduce(xs[1:]) )
```

How to implement sums/products of squares/cubes using this new function?

---

### Sums/products of squares/cubes (reprise)

```python
# xs: sequence of elements of type T
# f:  unary function  (type T => T)
# b:  binary function (type T x T => T)
#
# example: f(x) = x * x;  b(x, y) = x + y

def dry_reduce(xs, f, b):
  if len(xs) == 0: return 0
  return b( xs[0], dry_reduce(xs[1:]) )

dry_reduce(xs, lambda x: x * x, lambda y: y + y)   # sum of squares

dry_reduce(xs, lambda x: x * x, lambda y: y * y)   # product of squares

dry_reduce(xs, lambda x: x**3, lambda y: y + y)    # sum of cubes

dry_reduce(xs, lambda x: x**3, lambda y: y * y)    # product of cubes

```
