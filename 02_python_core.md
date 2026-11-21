# 02 --- Python Core

## 1. Strings

Strings are **immutable sequences of Unicode characters**. You can
access individual characters using indexing and extract parts using
slicing.

### Indexing and slicing

``` python
text = "Python"

print(text[0])     # P
print(text[-1])    # n
print(text[1:4])   # yth
print(text[::-1])  # nohtyP
```

- Positive indexing starts at `0`.
- Negative indexing starts from the end (`-1` is the last character).
- In slicing, the stop index is excluded.
- Slicing creates a new string.

### Common string methods

``` python
text = "  Hello Python  "

print(text.strip())                 # "Hello Python"
print(text.lower())                 # "  hello python  "
print(text.upper())                 # "  HELLO PYTHON  "
print(text.replace("Python", "API"))
print(text.split())                 # ["Hello", "Python"]
print(" ".join(["Hello", "Python"]))  # "Hello Python"
print(text.count("o"))
print(text.startswith("  He"))     # True
print(text.endswith("  "))         # True
print(text.find("Python"))          # index, or -1
```

| Method | Purpose |
| --- | --- |
| `strip()` | Removes leading and trailing whitespace. |
| `lower()` / `upper()` | Changes letter case. |
| `replace(old, new)` | Replaces occurrences of a substring. |
| `split(separator)` | Splits a string into a list. |
| `join(iterable)` | Joins strings using the string as a separator. |
| `count(value)` | Counts occurrences. |
| `startswith()` / `endswith()` | Checks a prefix or suffix. |
| `find()` | Returns the first index, or `-1` if not found. |
| `index()` | Returns the first index, or raises `ValueError` if not found. |

----------------------------------------------------------------------------------

### `split()` and `join()`

``` python
csv_line = "raj,,25,"
print(csv_line.split(","))  # ["raj", "", "25", ""]

words = ["backend", "developer"]
print(" ".join(words))      # backend developer
```

- `split()` without an argument treats consecutive whitespace as one
    separator and ignores leading/trailing whitespace.
- `split(",")` preserves empty values between consecutive delimiters
    and at the ends.
- `join()` is called on the separator string, and its input must
    contain strings.

### String validation

``` python
print("Python".isalpha())  # True
print("123".isdigit())     # True
print("abc123".isalnum())  # True
print("   ".isspace())     # True
```

These methods return booleans. Note that some Unicode characters have
surprising results; for example, `"²".isdigit()` is `True`.

### f-strings and formatting

``` python
name = "Sam"
salary = 75600.5
completion = 0.856

print(f"{name} earns {salary:,.2f}")
print(f"Completion: {completion:.2%}")
```

Output:

``` text
Sam earns 75,600.50
Completion: 85.60%
```

**Interview takeaway:** Strings are immutable. Methods such as `lower()`
and `replace()` return new strings; they do not modify the original
string.

## 2. Lists

A list is an **ordered, mutable collection** that allows duplicate
values and can contain different data types.

### Creating and accessing lists

``` python
items = ["pen", "book", "laptop", "book"]

print(items[0])    # pen
print(items[-1])   # book
print(items[1:3])  # ["book", "laptop"]
```

### Adding and updating elements

``` python
items = ["pen", "book"]

items.append("laptop")
items.extend(["mouse", "keyboard"])
items.insert(1, "notebook")

print(items)
```

| Method | Purpose |
| --- | --- |
| `append(value)` | Adds one value to the end. |
| `extend(iterable)` | Adds each element from an iterable. |
| `insert(index, value)` | Inserts a value at a position. |

``` python
values = [1, 2]
values.append([3, 4])
print(values)  # [1, 2, [3, 4]]

values = [1, 2]
values.extend([3, 4])
print(values)  # [1, 2, 3, 4]
```

### Removing elements

``` python
items = ["pen", "book", "laptop", "book"]

items.remove("book")  # Removes the first matching value
last_item = items.pop()  # Removes and returns the last value
del items[0]             # Deletes the element at index 0

print(items)
```

| Operation | Behaviour |
| --- | --- |
| `remove(value)` | Removes the first matching value; raises `ValueError` if absent. |
| `pop(index)` | Removes and returns the item; defaults to the last item. |
| `del items[index]` | Deletes an item by index. |
| `clear()` | Removes all elements. |

`pop()` with an invalid index raises `IndexError`.

### Sorting and reversing

``` python
numbers = [4, 1, 3, 2]

sorted_numbers = sorted(numbers)
descending = sorted(numbers, reverse=True)

numbers.sort()   # Mutates the original list
numbers.reverse()  # Reverses the original list
```

- `sorted(iterable)` returns a new sorted list.
- `list.sort()` sorts the list in place and returns `None`.
- `list.reverse()` reverses the list in place and returns `None`.

### Searching and counting

``` python
items = ["pen", "book", "pen"]

print("book" in items)  # True
print(items.count("pen"))  # 2
print(items.index("book"))  # 1
```

`index()` raises `ValueError` when the value is absent.

### Copying a list

``` python
original = [[1, 2], [3, 4]]
shallow = original.copy()

shallow[0].append(99)
print(original)  # [[1, 2, 99], [3, 4]]
```

`copy()` creates a shallow copy: the outer list is new, but nested
objects are shared. Use `copy.deepcopy()` when an independent nested
structure is required.

### Practical example: list of dictionaries

API responses often contain a list of records, where each record is a
dictionary.

``` python
employees = [
    {"name": "Raj", "salary": 70000},
    {"name": "Sam", "salary": 50000},
    {"name": "John", "salary": 80000}
]

high_earners = [
    employee["name"]
    for employee in employees
    if employee["salary"] >= 60000
]

print(high_earners)  # ["Raj", "John"]
```

Each loop variable (`employee`) refers to one dictionary in the list.

**Common pitfall:** A list of dictionaries is not itself a dictionary.
Iterate over the list first, then access keys on each dictionary.

**Interview takeaway:** Know the difference between `append()` and
`extend()`, mutating methods and functions that return new lists, and
shallow copies versus deep copies.

## 3. Tuples

A tuple is an **ordered, immutable collection**. It can contain
duplicate values and different data types.

### Creating tuples

``` python
empty = ()
single = (10,)  # A comma is required for a one-element tuple
not_a_tuple = (10)

print(type(single))      # <class 'tuple'>
print(type(not_a_tuple)) # <class 'int'>
```

### Accessing and unpacking

``` python
user = ("Sam", 25, "Bangalore")

name, age, city = user
print(name)  # Sam

first, *remaining = (10, 20, 30, 40)
print(first)      # 10
print(remaining)  # [20, 30, 40]
```

Tuples support indexing, slicing, membership checks, `count()` and
`index()`.

### Immutability nuance

A tuple's elements cannot be reassigned, but a mutable object stored
inside it can still be changed.

``` python
data = ([1, 2], 3)
data[0].append(4)

print(data)  # ([1, 2, 4], 3)
```

### List vs tuple

| List | Tuple |
| --- | --- |
| Mutable | Immutable |
| Uses `[]` | Uses `()` |
| Suitable for changing collections | Suitable for fixed records or grouped values |
| Usually not hashable | Can be hashable if all elements are hashable |

**Interview takeaway:** Use a list when the collection needs to change.
Use a tuple when the grouped values should remain fixed. A tuple is not
automatically hashable if it contains unhashable elements such as lists.

## 4. Dictionaries

A dictionary stores data as **key-value pairs**. Keys must be hashable
and unique; values can be of any type.

### Creating and accessing values

``` python
employee = {
    "name": "Sam",
    "salary": 50000
}

print(employee["name"])          # Sam
print(employee.get("department"))  # None
print(employee.get("department", "IT"))  # IT
```

- `dict[key]` raises `KeyError` if the key is missing.
- `dict.get(key)` returns `None` by default when the key is missing.
- `dict.get(key, default)` returns the supplied default without
    changing the dictionary.

### Adding, updating and removing

``` python
employee["department"] = "Engineering"
employee["salary"] = 60000

employee.update({"city": "Bangalore", "active": True})
removed_salary = employee.pop("salary")

del employee["city"]
```

### `get()` vs `setdefault()`

``` python
employee = {"name": "Sam"}

department = employee.get("department", "IT")
print(employee)  # {"name": "Sam"}

department = employee.setdefault("department", "IT")
print(employee)  # {"name": "Sam", "department": "IT"}
```

- `get()` reads a value or returns a fallback; it does not insert the
    key.
- `setdefault()` returns the existing value, or inserts the default if
    the key is absent.

### Iterating over a dictionary

``` python
employee = {"name": "Sam", "age": 25}

for key in employee:
    print(key)  # Iterates over keys

for value in employee.values():
    print(value)

for key, value in employee.items():
    print(key, value)
```

### Dictionary comprehension

``` python
names = ["Sam", "Raj", "John"]

name_lengths = {name: len(name) for name in names}
print(name_lengths)
# {"Sam": 3, "Raj": 3, "John": 4}
```

### Merging dictionaries

``` python
defaults = {"role": "user", "active": True}
updates = {"role": "admin"}

merged = defaults | updates
print(merged)  # {"role": "admin", "active": True}
```

The `|` merge operator is available in Python 3.9+. If keys overlap, the
right-hand dictionary's value wins.

**Interview takeaway:** Be comfortable with `get()`, `setdefault()`,
`.keys()`, `.values()`, `.items()`, dictionary comprehensions and
merging dictionaries.

## 5. Sets

A set is a **mutable collection of unique, hashable elements**. It does
not support indexing and does not guarantee a meaningful positional
order.

### Creating and updating sets

``` python
roles = {"admin", "user", "user"}
print(roles)  # {"admin", "user"}

roles.add("editor")
roles.update(["auditor", "viewer"])
```

Use `set()` to create an empty set. `{}` creates an empty dictionary.

### Removing set elements

```python
roles.remove("admin") # Raises KeyError if absent
roles.discard("guest") # Does nothing if absent
```

- `remove(value)` raises `KeyError` if the value is absent.
- `discard(value)` does not raise an error if the value is absent.
- `pop()` removes and returns an arbitrary element.
- `clear()` removes all elements.

### Set operations

``` python
frontend = {"Sam", "Raj", "John"}
backend = {"Raj", "John", "Mia"}

print(frontend | backend)  # Union
print(frontend & backend)  # Intersection
print(frontend - backend)  # Difference
print(frontend ^ backend)  # Symmetric difference
```

| Operator | Meaning |
| --- | --- |
| `A \| B` | Elements in either set (union). |
| `A & B` | Elements common to both (intersection). |
| `A - B` | Elements in A but not B (difference). |
| `A ^ B` | Elements in either set, but not both (symmetric difference). |

### Practical use: permissions

``` python
user_permissions = {"read", "write"}
required_permissions = {"read", "delete"}

has_all = required_permissions.issubset(user_permissions)
print(has_all)  # False
```

Membership checks in a set are average O(1), making sets useful for
checking whether an item has already been seen or whether a permission
exists.

**Interview takeaway:** Sets remove duplicates and provide efficient
membership checks. Set elements must be hashable; lists and dictionaries
cannot be elements of a set.

## 6. Iterables and Iterators

An **iterable** is an object that can return an iterator. An
**iterator** produces values one at a time and remembers its current
position.

### Iterable vs iterator

``` python
numbers = [10, 20, 30]

iterator = iter(numbers)

print(next(iterator))  # 10
print(next(iterator))  # 20
print(next(iterator))  # 30
```

- A list is iterable, but it is not itself an iterator.
- `iter(iterable)` returns an iterator.
- `next(iterator)` retrieves the next value.
- Calling `next()` after all values are consumed raises
    `StopIteration`.

### How a `for` loop works

A `for` loop obtains an iterator and repeatedly requests the next value.
It stops when the iterator raises `StopIteration`.

``` python
for number in [10, 20, 30]:
    print(number)
```

Conceptually, this is similar to:

``` python
iterator = iter([10, 20, 30])

while True:
    try:
        number = next(iterator)
        print(number)
    except StopIteration:
        break
```

Custom iterator classes implement `__iter__()` and `__next__()`.

**Interview takeaway:** An iterable can provide a fresh iterator, while
an iterator tracks progress and is generally consumed as values are
requested.

## 7. Generators

A generator is a convenient way to create an iterator. A **generator
function** uses `yield` to produce values one at a time, pausing its
execution between values.

### Generator function

``` python
def get_numbers():
    yield 10
    yield 20
    yield 30

numbers = get_numbers()

print(next(numbers))  # 10
print(next(numbers))  # 20
```

- Calling a generator function returns a generator object; the
    function body does not run to completion immediately.
- `yield` produces a value and pauses the function.
- The next `next()` call resumes execution from where it paused.
- When the function finishes, the generator is exhausted.

### `return` vs `yield`

- `return` ends the function and optionally sends back one result.
- `yield` produces a value while preserving the function's execution
    state for later.

### Generator expression vs list comprehension

``` python
squares_list = [x * x for x in range(5)]
squares_generator = (x * x for x in range(5))
```

| List Comprehension | Generator Expression |
| --- | --- |
| Creates the complete list immediately | Produces values lazily, as requested |
| Stores all results in memory | Avoids storing all results at once |
| Can be iterated over repeatedly | A generator is consumed as it is iterated |

### Understanding generator consumption

``` python
gen = (x * 10 for x in range(4))

print(next(gen))  # 0
print(next(gen))  # 10
print(list(gen))  # [20, 30]
print(list(gen))  # []
```

Each `next()` consumes one value. `list(gen)` consumes all remaining
values. Once exhausted, the same generator does not restart; create a
new generator to iterate again.

### Backend use cases

Generators are useful when processing large files, database records, or
API data in chunks. They let code process one item at a time instead of
loading every item into memory upfront.

**Interview takeaway:** Understand lazy evaluation, generator
exhaustion, `yield` vs `return`, and why generators can be useful for
memory-efficient processing.

## 8. Decorators

A decorator adds or modifies a function's behaviour **without changing
the original function's code**. A decorator is a callable that takes a
function and returns a function (or another callable).

### Basic decorator

``` python
def my_decorator(func):
    def wrapper():
        print("Before function")
        func()
        print("After function")
    return wrapper

@my_decorator
def greet():
    print("Hello!")

greet()
```

The decorator syntax:

``` python
@my_decorator
def greet():
    print("Hello!")
```

is equivalent to:

``` python
def greet():
    print("Hello!")

greet = my_decorator(greet)
```

### Decorators with arguments and return values

Use `*args` and `**kwargs` so the wrapper can accept the decorated
function's arguments. Return the original function's result if callers
need it.

``` python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        return result
    return wrapper

@log_call
def add(a, b):
    return a + b

print(add(10, 20))  # 30
```

If the wrapper does not return `result`, the decorated function call
returns `None` even if the original function returned a value.

### Preserve function metadata with `functools.wraps`

``` python
from functools import wraps

def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
```

`@wraps(func)` preserves metadata such as the original function's name
and docstring. It does not replace the need to return the function's
result.

### Decorators that accept their own arguments

A decorator with configuration needs an outer function that returns the
actual decorator.

``` python
def retry(max_retries):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except ValueError:
                    print(f"Attempt {attempt + 1} failed")

            raise RuntimeError("All retries failed")
        return wrapper
    return decorator

@retry(max_retries=3)
def call_service():
    # Example operation
    return "Success"
```

`@retry(max_retries=3)` first calls the decorator factory. The returned
decorator is then applied to `call_service`.

The `return` inside the `try` exits the wrapper immediately when the
function succeeds. In production code, retry only appropriate transient
errors and consider a delay/backoff strategy.

### Common decorator use cases

- Logging function calls.
- Measuring execution time.
- Authentication and authorization checks.
- Caching results.
- Retrying transient failures.

**Interview takeaway:** Know the roles of the decorator, wrapper,
`*args`/`**kwargs`, returned result, `functools.wraps`, and the extra
nesting required when a decorator accepts configuration.

## 9. Exception Handling

Exceptions represent errors that occur while a program is running.
Handling them allows the program to respond appropriately instead of
terminating unexpectedly.

### `try` and `except`

``` python
try:
    age = int("twenty")
except ValueError:
    print("Age must be a number")
```

Use specific exception types where possible.

### Multiple `except` blocks

``` python
try:
    number = int(input_value)
    result = 100 / number
except ValueError:
    print("Input must be a valid integer")
except ZeroDivisionError:
    print("Number cannot be zero")
```

### `else` and `finally`

``` python
try:
    result = 100 / number
except ZeroDivisionError:
    print("Cannot divide by zero")
else:
    print("Result:", result)
finally:
    print("Operation finished")
```

  | Block | When it runs |
| --- | --- |
| `try` | Contains code that may raise an exception. |
| `except` | Runs when a matching exception is raised. |
| `else` | Runs only when the `try` block completes without an exception. |
| `finally` | Runs whether an exception occurred or not; commonly used for cleanup. |

### Raising exceptions

Use `raise` when a function detects invalid input or a condition it
cannot handle.

``` python
def withdraw(balance, amount):
    if amount <= 0:
        raise ValueError("Amount must be positive")

    if amount > balance:
        raise ValueError("Insufficient balance")

    return balance - amount
```

### Custom exceptions

Custom exception classes make application errors more meaningful.

``` python
class InsufficientBalanceError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientBalanceError("Not enough balance")
```

### Exception chaining

Use `raise ... from ...` to preserve the original exception as the cause
of a higher-level error.

``` python
try:
    user_id = int(raw_user_id)
except ValueError as error:
    raise ValueError("Invalid user ID") from error
```

### Context managers and `with`

A context manager manages setup and cleanup around a block of code. For
example, `with open(...)` ensures that the file is closed when the block
exits, including when an exception occurs.

``` python
with open("users.txt", "r") as file:
    contents = file.read()
```

Context managers commonly implement `__enter__()` and `__exit__()`. The
`__exit__()` method can suppress an exception if it returns `True`;
otherwise, the exception continues to propagate.

**Interview takeaway:** Understand `try`/`except`/`else`/`finally`,
raising specific exceptions, custom exceptions, exception chaining, and
why context managers are useful for resource cleanup.

## 10. Modules and Packages

A **module** is a Python file (`.py`) containing reusable code. A
**package** organizes related modules into a directory structure.

### Example project structure

``` text
project/
    main.py
    calculator.py
    services/
        __init__.py
        payment.py
        notification.py
```

Here: - `calculator.py` is a module. - `services` is a package. -
`payment.py` and `notification.py` are modules inside the package.

### Importing modules

``` python
import calculator

calculator.add(10, 20)
```

Import selected functions:

``` python
from calculator import add, multiply

add(2, 3)
```

Use an alias:

``` python
import calculator as calc

calc.add(2, 3)
```

Import from a package:

``` python
from services.payment import process_payment
```

Do not include the `.py` extension or call the function with parentheses
in the import statement.

### `__init__.py`

A package commonly contains an `__init__.py` file. It can be empty or
contain package initialization code and selected exports. Modern Python
also supports namespace packages that do not require `__init__.py`.

### How Python finds modules

Python searches locations listed in `sys.path`, which commonly include
the script's directory, configured import paths, and installed package
locations.

**Interview takeaway:** Be able to distinguish modules from packages,
read common project structures, and write imports using the package and
module names rather than filenames with `.py`.

## Quick Interview Revision

| Question | Short Answer |
| --- | --- |
| Are strings mutable? | No. String methods return new strings. |
| `find()` vs `index()`? | `find()` returns `-1` when absent; `index()` raises `ValueError`. |
| `append()` vs `extend()`? | `append()` adds one object; `extend()` adds each item from an iterable. |
| `sort()` vs `sorted()`? | `sort()` mutates a list and returns `None`; `sorted()` returns a new list. |
| `remove()` vs `pop()`? | `remove()` deletes by value; `pop()` deletes by index and returns the item. |
| What does a shallow list copy do? | Creates a new outer list but shares nested objects. |
| What is a one-element tuple? | `(value,)` — the comma makes it a tuple. |
| `dict[key]` vs `dict.get(key)`? | Direct access raises `KeyError` if absent; `get()` returns a fallback. |
| `get()` vs `setdefault()`? | `get()` does not insert; `setdefault()` inserts the default when the key is absent. |
| What does a set guarantee? | Unique, hashable elements; no positional indexing. |
| Iterable vs iterator? | An iterable can provide an iterator; an iterator produces values and tracks its progress. |
| What does `next()` do? | Returns the next item; raises `StopIteration` when exhausted. |
| What does `yield` do? | Produces a value and pauses a generator function. |
| Are generators reusable? | A generator object is consumed once; create a new one to iterate again. |
| What is a decorator? | A callable that wraps or replaces a function to add behaviour. |
| What does `functools.wraps` preserve? | Function metadata such as name and docstring. |
| When does `else` run in exception handling? | Only when the `try` block completes without an exception. |
| When does `finally` run? | During normal or exceptional exit from the `try` statement. |
| What is a module? | A Python `.py` file containing reusable code. |
| What is a package? | A directory structure that organizes related modules. |
