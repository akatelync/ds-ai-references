Python Data Model
- the API that we use to make our own objects play well with the most idiomatic language features
- a description of Python as a framework
- formalizes the interfaces of the building blocks of the language itself, such as sequences, functions, iterators, coroutines, classes, context managers, etc.
- **The Python data model** is the internal API that lets you hook into the language’s syntax and behavior by implementing **special methods** (aka dunder methods).
	- it’s how you make your own classes behave like built-in types (like lists, dicts, numbers, etc.) — just by implementing specially named methods like `__len__`, `__getitem__`, or `__add__`.

## Classes
- when building new classes, the Python interpreter invokes special methods to perform basic object operations, often triggered by special syntax
	- special method names are always written with leading and trailing double underscores
	- `obj[key]` is supported by the `__get_item__` special method so in order to evaluate `my_collection[key]`, the interpreter calls `my_collection.__get_item__(key)`
- special methods are implemented when we want our objects to support and interact with fundamental language constructs such as:
	- collections
	- attribute access
	- iteration (including asynchronous iteration using `async for`)
	- operator overloading
	- function and method invocation
	- string representation and formatting
	- asynchronous programming using `await`
	- object creation and destruction
	- managed contexts using the `with` or `async with` statements
- special methods / magic methods: allow you to define how objects of a class interact with fundamental language features

## Language Constructs

| **Construct**                                  | **Description**                                                                                                          | **Relevant Special Methods**                                       | **Example Use Cases**                                   |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ | ------------------------------------------------------- |
| **1. Collections**                             | Data structures that store multiple items and support operations like checking memberships, getting length, or indexing. | `__len__`, `__getitem__`, `__setitem__`, `__contains__`            | `len(obj)`, `obj[key]`, `obj[key] = val`, `item in obj` |
| **2. Attribute Access**                        | Accessing, setting, or deleting object attributes using dot notation or dynamically.                                     | `__getattr__`, `__setattr__`, `__delattr__`                        | `obj.name`, `obj.name = value`, `del obj.name`          |
| **3. Iteration**<br>*(Including Async)*        | Looping over objects with `for` or `async for`.                                                                          | Regular: `__iter__`, `__next__`<br>Async: `__aiter__`, `__anext__` | `for item in obj`, `async for item in obj`              |
| **4. Operator Overloading**                    | Customize behavior of operators (`+`, `==`, `<`, etc.) for your objects.                                                 | `__add__`, `__eq__`, `__lt__`, etc.                                | `obj + other`, `obj == other`, `obj < other`            |
| **5. Function & Method Invocation**            | Make objects callable like functions using parentheses.                                                                  | `__call__`                                                         | `obj(*args, **kwargs)`                                  |
| **6. String Representation & Formatting**      | Define how objects are displayed or formatted as strings.                                                                | `__str__`, `__repr__`, `__format__`                                | `str(obj)`, `repr(obj)`, `f"{obj}"`                     |
| **7. Async Programming (`await`)**             | Make objects awaitable in async code.                                                                                    | `__await__`                                                        | `await obj` for async tasks like network calls          |
| **8. Object Creation & Destruction**           | Control how objects are created, initialized, and destroyed.                                                             | `__new__`, `__init__`, `__del__`                                   | `__init__` to set state, `__del__` to release resources |
| **9. Managed Contexts (`with`, `async with`)** | Manage setup and cleanup of resources in context blocks.                                                                 | `__enter__`, `__exit__`<br>`__aenter__`, `__aexit__`               | `with obj as x`, `async with obj as x`                  |

## FrenchDeck Example
- can make a custom object behave like a built-in sequence (like a list) just by implementing two special methods:
	- `__len__`: enables `len(deck)`
	- `__getitem__`: enables indexing, slicing, iteration, and even functions like `random.choice(deck)`
- this works without subclassing `list` and Python automatically uses these methods to provide full sequence behavior — including `for` loops, slicing and membership tests
- you can build rich, Pythonic objects by leveraging the **data model (dunder methods)** — writing less code while making your classes feel native to the language.


General Notes:
- `f-string` is better than `str.format()`
	- one reason to still use `my_fmt.format()` is when the definition of ` my_fmt` must be in a different place in the code than where the formatting operation needs to happen

## How Special Methods Are Used

|Rule|Why|
|---|---|
|✅ **Implement** special methods in your classes|So Python can integrate your object into its syntax|
|❌ Don’t **call** them directly|Use the related built-in functions instead|
|⚙️ Built-ins like `list` are faster because they bypass method calls internally|They use C structures like `ob_size` for performance|
|🧙 Only metaprogramming or class inheritance requires calling things like `__init__()` directly|Most code doesn't need this

### Built-in types are optimized
- these include `list`, `str`, `bytearray`
- written in C, not Python
- internally, they use a C data structure called `PyVarObject` with a field `ob_size` that directly holds the length
- so when you do `len(my_list)`, Python doesn't call a method it just reads that number
- much faster than calling a method like `__len__()`

### Special methods are usually called implicitly
Take this line:

```python
for i in x:
```

It looks simple, but behind the scenes, Python does:
```python
iter_x = iter(x)
```
Which internally does one of the following:
- If `x` has `__iter__()`, it calls that
- If not, it tries `__getitem__()` (like in the `FrenchDeck` example — where iteration is supported via indexing)
    
So even though you don’t see `__iter__()` being called — it **is** being used _implicitly_.

### Exception
Unless you're doing advanced stuff like metaprogramming, your code will mostly define special methods, not call them.

The one exception:  
➡️ You might call `__init__()` when you're manually initializing the superclass inside your own `__init__()`:

```python
class Child(Super):
    def __init__(self):
        Super.__init__(self)  # common use of calling a special method directly
```

### Prefer the built-in functions
If you need the functionality of a special method, call the built-in function that triggers it:
- Use `len(obj)` instead of `obj.__len__()`
- Use `str(obj)` instead of `obj.__str__()`
- Use `iter(obj)` instead of `obj.__iter__()`

Why?
- It’s cleaner and more idiomatic
- It sometimes does more than just call the special method (e.g., it may optimize performance for built-ins)

## Emulating Numeric Types
- Shows how to make a custom class behave like a number using operator overloading with special methods.
- Uses a simple 2-dimensional `Vector` class as an example.
- Implements methods to support:
    - `__add__` (for `v1 + v2`)
    - `__mul__` (for `v * scalar`)
    - `__abs__` (for `abs(v)`)
    - `__bool__` (for truth-testing, e.g., `if v:`)
    - `__repr__` (for readable representation)
        
### Example: The `Vector` Class

```python
from math import hypot

class Vector:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Vector({self.x!r}, {self.y!r})"

    def __abs__(self):
        return hypot(self.x, self.y)

    def __bool__(self):
        return bool(self.x or self.y)

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
```

With this implementation:

```python
v1 = Vector(2, 4)
v2 = Vector(2, 1)
v1 + v2         # → Vector(4, 5)

v = Vector(3, 4)
abs(v)           # → 5.0

v * 3            # → Vector(9, 12)
bool(Vector(0, 0))  # → False
```

No need to manually call `__add__` or `__abs__`; Python does it automatically via operators/functions 
### 🔑 Key Points

- **Operator overloading**: By implementing `__add__`, `__mul__`, you let Python’s syntax (`+`, `*`) work naturally with your objects.
    
- **Immutable-style behavior**: Operations return new `Vector` instances, keeping the originals unchanged—just like built-in numbers ([PagePlace](https://api.pageplace.de/preview/DT0400.9781491946268_A25607491/preview-9781491946268_A25607491.pdf?utm_source=chatgpt.com "[PDF] Fluent Python")).
    
- **Integration with built-ins**: `abs(v)` calls `__abs__`, `bool(v)` calls `__bool__`, so your type fits seamlessly with Python functions ([Thoughtworks](https://www.thoughtworks.com/content/dam/thoughtworks/documents/books/bk_fluent_python_2nd_edition_free_chapter_en.pdf?utm_source=chatgpt.com "[PDF] Fluent Python - Thoughtworks")).
    
- Python calls these methods **implicitly** — your code never needs to invoke them directly ([Thoughtworks](https://www.thoughtworks.com/content/dam/thoughtworks/documents/books/bk_fluent_python_2nd_edition_free_chapter_en.pdf?utm_source=chatgpt.com "[PDF] Fluent Python - Thoughtworks")).
    

---

### 🧾 TL;DR

|What You Do|Python Enables|
|---|---|
|Define `__add__`, `__mul__`, `__abs__`, `__bool__`, `__repr__`|Use standard operators (`+`, `*`) and built-ins (`abs`, `bool`, `repr`)|
|Return new objects in operators|Support natural, immutable behavior|
|Don’t call these methods directly|Python handles them behind the scenes|

**Bottom line**: Implementing a few key dunder methods turns your class into a full-fledged numeric type — clean, natural to use, and integrated with Python's built-in behavior.

Absolutely! Let's walk through the **String Representation** section from _Fluent Python_ and break it down in a clear, digestible way — with a summary at the end.

---

## String Representation

### 1. `__repr__` is for developers

- It's used when you inspect or debug an object.
- Called by:
    - `repr(obj)`
    - The interactive console
    - Debuggers
    - `%r` in `%`-style formatting
    - `!r` in f-strings like `f"{obj!r}"`
- If you don't define `__repr__`, Python will give you a default, unhelpful output like:
    ```
    <Vector object at 0x10e100070>
    ```

✅ Good practice: make `__repr__` return a string that looks like valid Python code to recreate the object (e.g., `Vector(3, 4)`), using `!r` for attributes:

```python
def __repr__(self):
    return f"Vector({self.x!r}, {self.y!r})"
```

### 2. `__str__` is for users
- It’s what gets shown when you:
    - Use `str(obj)`
    - Use `print(obj)`
- It should be friendly and readable — meant for end users, not debugging.
### 3. If you only define `__repr__`, that’s okay
- If `__str__` is missing, Python falls back to `__repr__`
- So if your `__repr__` is readable enough, you might not need to define `__str__` at all
### 4. Why use `!r` in `__repr__`?
- Using `!r` ensures that strings and numbers show up with quotes or without — the way Python would write them.
- This avoids subtle bugs or misleading representations (e.g., `"1"` vs `1`)
    
Example:
```python
Vector('3', '4')  # looks okay but actually invalid input (strings instead of numbers)
```

---

### 🧾 TL;DR

|Special Method|Purpose|Called by|Should return|
|---|---|---|---|
|`__repr__`|For debugging and developer inspection|`repr()`, REPL, `%r`, `!r`, debuggers|**Unambiguous**, ideally **re-creatable**|
|`__str__`|For user-friendly display|`print()`, `str()`|**Readable**, nice for end users|
|Fallback|If `__str__` is missing, `__repr__` is used|✅ That’s okay|Keep `__repr__` user-friendly when possible|
## Boolean Value of a Custom Type
### Python lets any object be used in a boolean context
- This includes:
	- `if obj:`
	- `while obj:`
	- `not obj`, `and`, `or`
- Even if the object isn’t literally a `True` or `False` boolean — Python will figure out whether it’s “truthy” or “falsy”.

### How Python decides what’s truthy or falsy
When you do `bool(x)`, Python checks in this order:
1. **If your object has a `__bool__()` method**:
    - Python uses that.
    - It must return a `True` or `False`.
2. **If `__bool__()` is not defined**, Python tries:
    - `__len__()`
    - If `len(x)` returns `0`, then `bool(x)` is `False`.
    - Otherwise, `True`.
3. **If neither is defined**, object is always considered truthy.

### Example: Vectors and Magnitude
Say you have a `Vector` class. You can define:

```python
def __bool__(self):
	return bool(abs(self))  # True if magnitude is not 0
```

So:
- `Vector(0, 0)` → `False`
- `Vector(3, 4)` → `True`

This gives your custom object logical behavior similar to built-in types like lists, numbers, etc.

### Why this matters
- Makes your objects behave intuitively in `if`, `while`, and other logic
- Keeps your classes clean, Pythonic, and predictable
    
And you almost never need to call `bool()` directly — Python does it for you under the hood in logical expressions.

### 🧾TL;DR
| What            | How Python decides truthiness                                         |
| --------------- | --------------------------------------------------------------------- |
| Custom class    | `bool(obj)` checks:                                                   |
| 1. `__bool__()` | If present, use it — must return `True` or `False`                    |
| 2. `__len__()`  | If `__bool__` is missing, check length: 0 = `False`, otherwise `True` |
| 3. Default      | If neither is present, object is truthy                               |
## Collection API
![[Pasted image 20250720132306.png]]
UML class diagram with fundamental collection types. Method names in italic are abstract, so they must be implemented by concrete subclasses such as list and dict. The remaining methods have concrete implementations, therefore subclasses can inherit them.

### Abstract Base Classes (ABCs) form the backbone
- **Collection** (introduced in Python 3.6) sits at the top and defines a minimal interface:
    - `__len__()` → sized to support the `len` built-in function
    - `__iter__()` → iterable to support `for`, `unpacking`, and other forms of iteration
    - `__contains__()` → container to support the `in` operator
- Subclasses inherit behaviors from `Collection` and add on specific capabilities.

### Key ABCs and their roles
| ABC Class    | Special Method                  | Purpose                         |
| ------------ | ------------------------------- | ------------------------------- |
| `Collection` | **len**, **iter**, **contains** | size, iteration, membership     |
| `Sequence`   | **getitem**, (plus inherited)   | ordered, index-accessible       |
| `Mapping`    | **getitem**, keys(), etc.       | key-value stores (e.g., dict)   |
| `Set`        | **and**, **or**, **sub**, etc.  | set operations, unique elements |
| `Reversible` | **reversed**                    | supports `reversed()` built-in  |
- Only `Sequence` supports _reversal_ because it has defined ordering.
- `Set` defines various operators like union (`|`), intersection (`&`), etc., using special methods.

### Concrete built-in types realize these ABCs
- `list`, `str`, `tuple` → `Sequence`
- `dict`, `defaultdict` → `Mapping`
- `set`, `frozenset` → `Set`  

These built-ins don’t have to explicitly inherit from the ABCs—if they implement the required dunder methods, they satisfy the interface.

### Why This Matters
1. **Minimal API requirement**  
    You only need to implement one or two special methods (e.g., `__getitem__` for sequence). Python provides the rest.
2. **Consistency across collections**  
    Everything follows the same interface—so you can write generic code handling sequences, mappings, or sets.
3. **Composition through inheritance**  
    By inheriting from ABCs, you gain rich and tested behaviors (like iteration, resizing, or containment) without reinventing the wheel.
4. **Protocol-based typing**  
    Python uses _duck typing_: if your object has `__iter__`, `__len__`, and `__contains__`, it’s treated as a `Collection`, even if you don’t explicitly subclass it.

### TL;DR
|**Abstract Base Class (ABC)**|**Required Dunder Methods**|**What It Represents**|**Examples of Built-in Types**|
|---|---|---|---|
|`Collection`|`__len__`, `__iter__`, `__contains__`|Core interface: size, iteration, membership testing|`list`, `set`, `dict`, `tuple`, etc.|
|`Sequence`|`__getitem__`, `__len__`|Ordered collection with index access|`list`, `tuple`, `str`|
|`Mapping`|`__getitem__`, `keys`, `__iter__`, etc.|Key-value pairs|`dict`, `defaultdict`|
|`Set`|`__contains__`, `__iter__`, `__len__` + set ops (`__and__`, etc.)|Unique unordered elements with math-like ops|`set`, `frozenset`|
|`Reversible`|`__reversed__`|Supports reverse iteration|`list`, `tuple`, `str`|
- Implement just the **core methods**, and ABCs fill in the rest.
- You can **mimic built-in collection behavior** by inheriting or matching these ABCs.
- Python uses **protocols, not inheritance**, to recognize your custom collections.

### What does “protocols, not inheritance” mean?
In Python, **you don’t need to explicitly inherit from a class** like `collections.abc.Sequence` for your object to behave like a sequence. Instead:
> If your class **implements the right methods**, Python will treat it as if it _is_ a sequence (or set, or mapping, etc.).

This is called **duck typing**:
> _"If it walks like a duck and quacks like a duck, it’s a duck."_


## Overview of Special Methods
Table 1-1 shows special method names, excluding those used to implement infix operators or core math functions like `abs`

| **Category**                      | **Special Method Names**                                                   | **Definition / Use**                                                                                                 |
| --------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **String/bytes representation**   | `__repr__`, `__str__`, `__format__`, `__bytes__`, `__fspath__`             | Controls how objects are converted to string or bytes, e.g. for debugging, display, formatting, or filesystem paths. |
| **Conversion to number**          | `__bool__`, `__complex__`, `__int__`, `__float__`, `__hash__`, `__index__` | Lets your object behave like a number (for casting, hashing, indexing, or truth-value testing).                      |
| **Emulating collections**         | `__len__`, `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`     | Makes your object behave like a list, dict, or other container (indexing, length, membership).                       |
| **Iteration**                     | `__iter__`, `__aiter__`, `__next__`, `__anext__`, `__reversed__`           | Enables iteration with `for` and `async for` loops; defines how to get and move through items.                       |
| **Callable/coroutine execution**  | `__call__`, `__await__`                                                    | Lets your object behave like a function or awaitable coroutine.                                                      |
| **Context management**            | `__enter__`, `__exit__`, `__aenter__`, `__aexit__`                         | Enables usage in `with` or `async with` blocks for managing resources (e.g. files, DB connections).                  |
| **Instance creation/destruction** | `__new__`, `__init__`, `__del__`                                           | Controls how objects are created, initialized, and finalized (destructed).                                           |
| **Attribute management**          | `__getattr__`, `__getattribute__`, `__setattr__`, `__delattr__`, `__dir__` | Manages how attributes are accessed, set, deleted, or listed.                                                        |
| **Attribute descriptors**         | `__get__`, `__set__`, `__delete__`, `__set_name__`                         | Defines descriptor behavior, often used in properties and class-level attribute customization.                       |
| **Abstract base classes**         | `__instancecheck__`, `__subclasscheck__`                                   | Lets you customize `isinstance()` and `issubclass()` checks.                                                         |
| **Class metaprogramming**         | `__prepare__`, `__init_subclass__`, `__class_getitem__`, `__mro_entries__` | Hooks for customizing behavior when classes are created, subclassed, or used with generics.                          |

Infix and numerical operators are supported by the special methods listed in Table 1-2

| Operator Category                   | Symbols / Functions                                                      | Special Method Names                                                                                                          |
| ----------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **Unary numeric**                   | `-`, `+`, `abs()`                                                        | `__neg__`, `__pos__`, `__abs__`                                                                                               |
| **Rich comparison**                 | `<`, `<=`, `==`, `!=`, `>`, `>=`                                         | `__lt__`, `__le__`, `__eq__`, `__ne__`, `__gt__`, `__ge__`                                                                    |
| **Arithmetic**                      | `+`, `-`, `*`, `/`, `//`, `%`, `@`, `divmod()`, `round()`, `**`, `pow()` | `__add__`, `__sub__`, `__mul__`, `__truediv__`, `__floordiv__`, `__mod__`, `__matmul__`, `__divmod__`, `__round__`, `__pow__` |
| **Reversed arithmetic**             | (swapped operands)                                                       | `__radd__`, `__rsub__`, `__rmul__`, `__rtruediv__`, `__rfloordiv__`, `__rmod__`, `__rmatmul__`, `__rdivmod__`, `__rpow__`     |
| **Augmented assignment arithmetic** | `+=`, `-=`, `*=`, `/=`, `//=`, `%=`, `@=`, `**=`                         | `__iadd__`, `__isub__`, `__imul__`, `__itruediv__`, `__ifloordiv__`, `__imod__`, `__imatmul__`, `__ipow__`                    |
| **Bitwise**                         | `&`, `\|`, `^`, `<<`, `>>`, `~`                                          | `__and__`, `__or__`, `__xor__`, `__lshift__`, `__rshift__`, `__invert__`                                                      |
| **Reversed bitwise**                | (swapped operands)                                                       | `__rand__`, `__ror__`, `__rxor__`, `__rlshift__`, `__rrshift__`                                                               |
| **Augmented bitwise assignment**    | `&=`, `\|=`, `^=`, `<<=`, `>>=`                                          | `__iand__`, `__ior__`, `__ixor__`, `__ilshift__`, `__irshift__`                                                               |

## Why len is not a method
- Python favors **built-in functions** like `len(obj)` over calling methods like `obj.len()` because this keeps interfaces **consistent** and **simpler** to implement and use across different types (e.g., lists, tuples, custom classes).
- The idea is: "Do not require methods for things that can be handled generically by the interpreter."

