# Lesson 3: Class Methods and Static Methods

[Previous: Lesson 2](../2-Class-Instance-Variables/) | [Next: Lesson 4](../4-Inheritance/README.md)

An `Employee` method does not always need to work with one particular
employee. Some behavior belongs to the class as a whole, while other behavior
only has a logical connection to the class. Class methods and static methods
handle those two cases.

## Step-by-Step Tutorial

### 1. Begin with regular instance methods

Begin with the existing `Employee` class and two employees:

```python
class Employee:

    num_of_emps = 0
    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@email.com'
        self.pay = pay

        Employee.num_of_emps += 1

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amt)


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'Employee', 60000)
```

A regular method automatically receives the instance as its first argument,
conventionally named `self`. That is appropriate for `fullname()` and
`apply_raise()`, which work with one employee. Changing a setting shared by
the whole class calls for a different kind of method.

### 2. Create `set_raise_amt()` as a class method

Create `set_raise_amt()` and begin with `pass`:

```python
@classmethod
def set_raise_amt(cls, amount):
    pass
```

The `@classmethod` decorator makes the method receive the class, rather than
an instance, as its first argument. By convention, this argument is named
`cls`. The name `class` cannot be used because it is a Python keyword.

Now use that class argument to set the shared raise amount:

```python
@classmethod
def set_raise_amt(cls, amount):
    cls.raise_amt = amount
```

Before the method is called, the class and both instances find the original
value:

```python
print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)
```

```text
1.04
1.04
1.04
```

Next, set the raise amount to 5 percent through the class:

```python
Employee.set_raise_amt(1.05)

print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)
```

```text
1.05
1.05
1.05
```

Because `cls` is `Employee` in this call, `cls.raise_amt = amount` has the same
effect as `Employee.raise_amt = 1.05`. Both instances now find the updated
class value as well.

A class method can also be called from an instance:

```python
emp_1.set_raise_amt(1.05)
```

Even then, the method receives the class and changes the class variable.
Calling it from the class makes that behavior clearer.

### 3. Parse an employee string manually

Class methods can do more than change class variables. To see another need
for them, suppose employee data arrives in three hyphen-separated strings:

```python
emp_str_1 = 'John-Doe-70000'
emp_str_2 = 'Steve-Smith-30000'
emp_str_3 = 'Jane-Doe-90000'
```

Creating an employee from the first string requires splitting it before
passing the values to `Employee`:

```python
first, last, pay = emp_str_1.split('-')

new_emp_1 = Employee(first, last, pay)

print(new_emp_1.email)
print(new_emp_1.pay)
```

```text
John.Doe@email.com
70000
```

This works, but the same parsing would have to be repeated whenever an
employee is created from this format. The split values are strings, so `pay`
is stored as the string `'70000'`; printing it displays `70000` without
quotes.

### 4. Add the `from_string()` alternative constructor

A class method can move that repeated parsing into `Employee` and provide an
alternative way to construct an object. Alternative constructors commonly
begin with `from`, so add `from_string()`:

```python
@classmethod
def from_string(cls, emp_str):
    first, last, pay = emp_str.split('-')
    return cls(first, last, pay)
```

The method splits the supplied string, passes the resulting values to `cls`,
and returns the new employee. The caller can now replace the manual parsing
and constructor call with:

```python
#new_emp_1 = Employee(first, last, pay)
new_emp_1 = Employee.from_string(emp_str_1)

print(new_emp_1.email)
print(new_emp_1.pay)
```

```text
John.Doe@email.com
70000
```

The `datetime` module uses the same idea for alternative ways to create
objects, including `fromtimestamp()` and `today()`.

### 5. Create the `is_workday()` static method

Now consider behavior that belongs near `Employee` but needs neither an
employee instance nor the class. A static method receives neither one
automatically. It behaves like a regular function placed in the class because
of a logical connection.

Checking whether a date is a workday fits that description. It relates to
employees, but it does not use an employee instance or an `Employee` class
variable:

```python
@staticmethod
def is_workday(day):
    if day.weekday() == 5 or day.weekday() == 6:
        return False
    return True
```

For `weekday()`, Monday is `0`, Saturday is `5`, and Sunday is `6`. The method
returns `False` for either weekend value and `True` for the remaining days.

Begin with Sunday, July 10, 2016:

```python
import datetime
my_date = datetime.date(2016, 7, 10)

print(Employee.is_workday(my_date))
```

```text
False
```

The result is `False` because Sunday is not a workday. Next, change the date to
Monday, July 11, 2016:

```python
my_date = datetime.date(2016, 7, 11)

print(Employee.is_workday(my_date))
```

```text
True
```

### 6. Identify a possible static method

The distinction now follows from what the method needs. A regular method uses
the instance through `self`, and a class method uses the class through `cls`.
If neither argument is used, the method may belong as a static method instead.

[Previous: Lesson 2](../2-Class-Instance-Variables/) | [Next: Lesson 4](../4-Inheritance/README.md)
