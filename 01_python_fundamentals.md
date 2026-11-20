# 01 --- Python Fundamentals

## 1. Variables, Names, and References

Python variables are **names bound to objects**. A variable does not
contain the object itself; it refers to an object.

``` python
a = [1, 2, 3]
b = a

b.append(4)

print(a)  # [1, 2, 3, 4]
print(b)  # [1, 2, 3, 4]
```

Both `a` and `b` refer to the same list.

### Reassignment vs mutation

``` python
a = [1, 2, 3]
b = a

b = b + [4]       # creates a new list and rebinds b

print(a)  # [1, 2, 3]
print(b)  # [1, 2, 3, 4]
```

- **Mutation** changes an existing mutable object.
- **Reassignment** binds a name to another object.

### Copying a list

``` python
a = [1, 2, 3]
b = a.copy()

b.append(4)

print(a)  # [1, 2, 3]
print(b)  # [1, 2, 3, 4]
```

`list.copy()` creates a shallow copy (see Section 4).

**Interview takeaway:** Understand whether code mutates an object or
reassigns a variable. This is especially important when lists or
dictionaries are passed to functions.

## 2. Mutable vs. Immutable Objects

An object's mutability describes whether its contents or state can be changed after creation.

| Common Mutable Types | Common Immutable Types |
| --- | --- |
| `list` | `int` |
| `dict` | `float` |
| `set` | `bool` |
| Most user-defined instances | `str` |
| | `tuple` (if its elements do not themselves mutate) |
| | `frozenset` |

### Example

``` python
a = [1, 2]
a.append(3)
print(a)  # [1, 2, 3]

x = 10
y = x
x = 20

print(x)  # 20
print(y)  # 10
```

The list is mutated in place. In the second example, `x = 20` rebinds
`x`; it does not modify the integer object `10`.

**Important nuance:** An immutable container such as a tuple can contain
a mutable object:

``` python
t = ([1, 2], 3)
t[0].append(4)

print(t)  # ([1, 2, 4], 3)
```

The tuple's references cannot be reassigned, but the nested list can
still be mutated.

**Interview takeaway:** Mutability affects aliasing, function arguments,
copying, and whether an object can be used as a dictionary key or set
element. Hashability is related but is not exactly the same concept.

## 3. `==` vs `is`

- `==` checks **value equality**.
- `is` checks **object identity**: whether two names refer to the
    exact same object.

``` python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)  # True
print(a is b)  # False
print(a is c)  # True
```

Use `is` for identity checks, especially comparisons with `None`:

``` python
if result is None:
    print("No result")
```

Do not use `is` when you mean ordinary value equality.

**Interview takeaway:** Two different objects can compare equal with
`==` while `is` is `False`.

## 4. Shallow Copy vs Deep Copy

A **shallow copy** creates a new outer container but keeps references to
the original nested objects.

A **deep copy** recursively copies nested objects as well.

``` python
import copy

a = [[1, 2], [3, 4]]

b = copy.copy(a)
c = copy.deepcopy(a)

b[1].append(50)
c[0].append(100)

print(a)  # [[1, 2], [3, 4, 50]]
print(b)  # [[1, 2], [3, 4, 50]]
print(c)  # [[1, 2, 100], [3, 4]]
```

| Operation | New Outer List? | Nested Objects Shared? |
| --- | --- | --- |
| `b = a` | No | Yes — same outer object too |
| `a.copy()` / `copy.copy(a)` | Yes | Yes |
| `copy.deepcopy(a)` | Yes | Normally no — recursively copies nested objects |

### When to Use

- **Shallow copy:** When a new outer collection is enough, or nested objects are intentionally shared.
- **Deep copy:** When an independent nested structure is needed.

> **Caution:** Deep copying can be expensive and may not be appropriate for every object. Custom objects can define their own copy behavior.

## 5. How Python Passes Function Arguments

Python uses **object sharing** (also called call by sharing). A function
parameter becomes a local name referring to the object passed by the
caller.

### Mutating a passed object

``` python
def add_item(items):
    items.append(4)

values = [1, 2, 3]
add_item(values)

print(values)  # [1, 2, 3, 4]
```

The function mutates the same list object.

### Reassigning the local parameter

``` python
def replace_items(items):
    items = [10, 20]

values = [1, 2, 3]
replace_items(values)

print(values)  # [1, 2, 3]
```

Reassignment changes only the local parameter binding. It does not
rebind the caller's variable.

**Interview takeaway:** Passing a mutable object does not automatically
copy it. Mutations can be visible to the caller; local reassignment is
not.

## 6. Mutable Default Argument Pitfall

Default argument expressions are evaluated **once when the function is
defined**, not every time it is called.

### The pitfall

``` python
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item(10))  # [10]
print(add_item(20))  # [10, 20] — same default list reused
```

### Preferred fix

Use `None` as the default and create a new list inside the function:

``` python
def add_item(item, items=None):
    if items is None:
        items = []

    items.append(item)
    return items

print(add_item(10))  # [10]
print(add_item(20))  # [20]
```

If a caller explicitly passes an existing list, the function still
mutates that list:

``` python
values = [10]
result = add_item(30, values)

print(values)        # [10, 30]
print(result is values)  # True
```

**Interview takeaway:** Avoid mutable objects such as lists and
dictionaries as default parameter values unless shared state is
intentional.

## 7. LEGB Scope Rule

Python resolves a name using the **LEGB** lookup order:

1. **L --- Local:** current function.
2. **E --- Enclosing:** enclosing function(s).
3. **G --- Global:** module-level scope.
4. **B --- Built-in:** Python's built-in names, such as `len` and
    `print`.

``` python
x = "global"

def outer():
    x = "enclosing"

    def inner():
        x = "local"
        print(x)

    inner()

outer()  # local
```

If `inner()` did not define its own `x`, Python would find `x` in the
enclosing function.

**Interview takeaway:** A local name can shadow an enclosing or global
name.

## 8. `global` vs `nonlocal`

Both keywords allow assignment to a name outside the current local
scope, but they target different scopes.

### `global`

Refers to a module-level name.

``` python
x = 100

def update():
    global x
    x = 200

update()
print(x)  # 200
```

### `nonlocal`

Refers to a name in the nearest enclosing function scope.

``` python
def outer():
    x = 10

    def inner():
        nonlocal x
        x = 20

    inner()
    print(x)  # 20

outer()
```

### Key Difference

| Keyword | Target Scope |
| --- | --- |
| `global` | Module-level scope |
| `nonlocal` | Nearest enclosing function scope |

**Common pitfall:** `global` does not mean "the variable in the parent
function." Use `nonlocal` for that.

## 9. `*args` and `**kwargs`

These let a function accept a variable number of arguments.

- `*args` collects extra positional arguments into a **tuple**.
- `**kwargs` collects extra keyword arguments into a **dictionary**.

``` python
def display(a, b, *args, **kwargs):
    print(a)
    print(b)
    print(args)
    print(kwargs)

display(10, 20, 30, 40, name="Sam", age=25)
```

Output:

``` text
10
20
(30, 40)
{'name': 'Sam', 'age': 25}
```

### Unpacking arguments

``` python
def add(a, b):
    return a + b

numbers = [10, 20]
print(add(*numbers))  # 30

values = {"a": 10, "b": 20}
print(add(**values))  # 30
```

- `*` unpacks an iterable into positional arguments.
- `**` unpacks a mapping into keyword arguments.

### Accessing keyword arguments safely

``` python
def greet(**kwargs):
    name = kwargs.get("name", "Guest")
    print(name)

greet(age=25)  # Guest
```

- `kwargs["name"]` raises a `KeyError` if the key is missing.
- `kwargs.get("name")` returns `None` by default.
- `kwargs.get("name", "Guest")` returns `"Guest"` if the key is missing.

**Interview takeaway:** Know the difference between collecting arguments
(`*args`, `**kwargs`) and unpacking them (`*`, `**`).

## 10. Lambda Functions

A lambda creates a small anonymous function containing a single
expression. The expression's value is returned automatically.

``` python
multiply = lambda a, b: a * b

print(multiply(3, 4))  # 12
```

Equivalent regular function:

``` python
def multiply(a, b):
    return a * b
```

Lambdas are useful for short functions, often as arguments to functions
such as `map`, `filter`, and `sorted`.

**Caution:** Prefer `def` when logic is complex or needs multiple
statements, documentation, or a meaningful function name.

## 11. `map()` and `filter()`

Both return iterators in Python 3, so convert them to a list when you
want to see all results immediately.

### `map()`: transform each item

``` python
numbers = [1, 2, 3, 4, 5]

squares = list(map(lambda x: x ** 2, numbers))
print(squares)  # [1, 4, 9, 16, 25]
```

### `filter()`: keep items that pass a condition

``` python
numbers = [1, 2, 3, 4, 5]

large_numbers = list(filter(lambda x: x > 2, numbers))
print(large_numbers)  # [3, 4, 5]
```

| Function | Purpose |
| --- | --- |
| `map(function, iterable)` | Applies a transformation to each item. |
| `filter(predicate, iterable)` | Keeps items for which the predicate is truthy. |

**Interview takeaway:** `map` changes/transforms values; `filter`
selects which values remain.

## 12. List Comprehensions

A list comprehension is a concise way to create a list from an iterable.

### Basic syntax

``` python
[expression for item in iterable]
```

Equivalent loop:

``` python
numbers = [1, 2, 3, 4, 5]
squares = []

for x in numbers:
    squares.append(x ** 2)

print(squares)  # [1, 4, 9, 16, 25]
```

List comprehension:

``` python
numbers = [1, 2, 3, 4, 5]
squares = [x ** 2 for x in numbers]

print(squares)  # [1, 4, 9, 16, 25]
```

### With a condition

``` python
numbers = [1, 2, 3, 4, 5, 6]

evens = [x for x in numbers if x % 2 == 0]
print(evens)  # [2, 4, 6]
```

General syntax:

``` python
[expression for item in iterable if condition]
```

### Transform and filter together

``` python
numbers = [1, 2, 3, 4, 5, 6]

result = [x ** 2 for x in numbers if x % 2 == 0]
print(result)  # [4, 16, 36]
```

Read it as:

1. Iterate through each number.
2. Keep only even numbers.
3. Square each retained number.

### Nested list comprehension: flatten a matrix

``` python
matrix = [[1, 2], [3, 4], [5, 6]]

flattened = [x for row in matrix for x in row]
print(flattened)  # [1, 2, 3, 4, 5, 6]
```

This is equivalent to:

``` python
flattened = []

for row in matrix:
    for x in row:
        flattened.append(x)
```

### Preserve the nested structure

``` python
matrix = [[1, 2], [3, 4]]

doubled = [[x * 2 for x in row] for row in matrix]
print(doubled)  # [[2, 4], [6, 8]]
```

The inner comprehension processes each row. The outer comprehension
builds a list containing the transformed rows.

**Interview takeaway:** Pay attention to the order of `for` clauses. A
comprehension with one output expression over all elements may flatten a
nested collection; a nested comprehension can preserve its row
structure.

## Quick Interview Revision

| Question | Short Answer |
| --- | --- |
| What happens with `b = a` for a list? | Both names refer to the same list object. |
| Mutation vs. reassignment? | Mutation changes an object; reassignment binds a name to another object. |
| `==` vs. `is`? | Value equality vs. object identity. |
| Shallow vs. deep copy? | Shallow copies the outer container; deep copy recursively copies nested objects. |
| How are arguments passed in Python? | Object sharing: parameters refer to the passed objects. |
| Why avoid `items=[]` as a default? | The same default list is reused across calls. |
| What is LEGB? | Local, Enclosing, Global, Built-in name lookup. |
| `global` vs. `nonlocal`? | Module scope vs. nearest enclosing function scope. |
| What are `args` and `kwargs`? | Extra positional arguments as a tuple; extra keyword arguments as a dictionary. |
| What does `map()` do? | Transforms each item. |
| What does `filter()` do? | Selects items whose predicate is truthy. |
| What is a list comprehension? | A concise way to build a list from an iterable, optionally with conditions. |
