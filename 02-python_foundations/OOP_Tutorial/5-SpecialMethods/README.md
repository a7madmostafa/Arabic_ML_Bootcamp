# Lesson 5: Special (Dunder) Methods

[Previous: Lesson 4 - Inheritance](../4-Inheritance/) | [Next: Lesson 6 - Property Decorators](../6-property-decorator/README.md)

Python uses special methods to give objects built-in behavior, such as a
readable display, addition, and length. These methods also make operator
overloading possible. Their names begin and end with double underscores, so
they are often called dunder methods.

## Step-by-Step Tutorial

### 1. Compare Integer and String Addition

Begin with the `+` operator. Its behavior depends on the objects on either
side:

```python
print(1 + 2)
print('a' + 'b')
```

Output:

```text
3
ab
```

Integers are added, while strings are concatenated. Python gets this
type-specific behavior from special methods. An `Employee` class can define
its own special methods in the same way.

### 2. Start with Employee

```python
class Employee:

    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@email.com'
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amt)


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'Employee', 60000)

print(emp_1)
```

The starting `Employee` class already has regular class behavior, but printing
an instance gives only a vague object representation. The address varies:

```text
<__main__.Employee object at 0x...>
```

There is already one special method in the class: `__init__`. Python calls it
when an employee is created, and it sets the new object's attributes. Other
special methods can change what Python does when the object is printed or used
with an operator.

### 3. Add `__repr__`

First, add `__repr__` to replace the vague display with an unambiguous
representation for developers. A useful `__repr__` looks like the code needed
to recreate the object:

```python
def __repr__(self):
    return "Employee('{}', '{}', {})".format(self.first, self.last, self.pay)
```

```python
print(emp_1)
```

Output:

```text
Employee('Corey', 'Schafer', 50000)
```

With no `__str__` method yet, Python falls back to `__repr__` when it needs a
string display.

### 4. Add `__str__`

Next, add `__str__` for a more readable, end-user display:

```python
def __str__(self):
    return '{} - {}'.format(self.fullname(), self.email)
```

```python
print(emp_1)
```

Output:

```text
Corey Schafer - Corey.Schafer@email.com
```

Now each representation can be requested separately:

```python
print(repr(emp_1))
print(str(emp_1))
```

Output:

```text
Employee('Corey', 'Schafer', 50000)
Corey Schafer - Corey.Schafer@email.com
```

The `repr()` and `str()` calls invoke the corresponding special methods.
Calling those methods directly produces the same output:

```python
print(emp_1.__repr__())
print(emp_1.__str__())
```

Output:

```text
Employee('Corey', 'Schafer', 50000)
Corey Schafer - Corey.Schafer@email.com
```

### 5. Call Built-in `__add__` Methods

The earlier integer and string results come from each type's own `__add__`
method. Calling those methods directly makes the connection clear:

```python
print(1 + 2)
print(int.__add__(1, 2))

print('a' + 'b')
print(str.__add__('a', 'b'))
```

Output:

```text
3
3
ab
ab
```

### 6. Add Employees with `__add__`

The same mechanism can define addition for employees. Here, adding two
employees will return their combined pay:

```python
def __add__(self, other):
    return self.pay + other.pay
```

`self` is the employee on the left side of `+`, and `other` is the employee on
the right side. With `__add__` in place, the operator now has a meaning for two
`Employee` objects:

```python
print(emp_1 + emp_2)
```

Output:

```text
110000
```

### 7. Compare String Length

Length follows the same pattern. The `len()` function calls an object's
`__len__` method:

```python
print(len('test'))
print('test'.__len__())
```

Output:

```text
4
4
```

### 8. Add Employee Length with `__len__`

To make `len()` work with an employee, define `__len__` to return the number
of characters in the employee's full name:

```python
def __len__(self):
    return len(self.fullname())
```

```python
print(len(emp_1))
```

Output:

```text
13
```

### 9. Inspect `datetime` Special Methods

These methods are not limited to custom classes. The `datetime` module uses
the same ideas in its `timedelta` and `date` classes:

- `timedelta.__add__` first checks whether the other object is another
  `timedelta`. If it is, the method returns a new `timedelta` with the days,
  seconds, and microseconds from both objects added together.
- If the other object is unsupported, `timedelta.__add__` returns
  `NotImplemented` instead of immediately raising an error. This gives the
  other operand a chance to handle the operation. If neither operand can
  handle it, Python raises an error.
- The `date` class defines `__repr__` with the same principle used for
  `Employee`: its result looks like the code that creates a date object.
- The `date` class sets `__str__` to its ISO format function, so printing a
  date produces its ISO-formatted value.

[Previous: Lesson 4 - Inheritance](../4-Inheritance/) | [Next: Lesson 6 - Property Decorators](../6-property-decorator/README.md)
