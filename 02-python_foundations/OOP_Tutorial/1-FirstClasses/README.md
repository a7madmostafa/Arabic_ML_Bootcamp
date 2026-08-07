# Lesson 1: Classes and Instances

[Course home](../README.md) | [Next lesson: Class and Instance Variables](../2-Class-Instance-Variables/README.md)

## Step-by-Step Tutorial

### 1. Create an empty class

Suppose a company needs to represent its employees in Python. Each employee has data, such as a name, email address, and pay, along with actions that use that data. A class keeps this related data and functionality together so the same structure can be reused and built upon. Data associated with a class is called attributes, and functions associated with a class are called methods.

Begin with an empty `Employee` class:

```python
class Employee:
    pass
```

An empty class body would cause an error, so `pass` tells Python to skip it for now.

### 2. Create two instances

The class is a blueprint for creating employees. Each employee created from that blueprint is a separate instance:

```python
class Employee:
    pass


emp_1 = Employee()
emp_2 = Employee()

print(emp_1)
print(emp_2)
```

Both objects come from `Employee`, but their different memory locations show that they are unique instances:

```text
<__main__.Employee object at 0x...>
<__main__.Employee object at 0x...>
```

### 3. Set instance variables manually

Each employee now needs its own data. Instance variables hold values that are unique to a particular instance, so they can be assigned directly to `emp_1` and `emp_2`:

```python
emp_1.first = 'Corey'
emp_1.last = 'Schafer'
emp_1.email = 'Corey.Schafer@company.com'
emp_1.pay = 50000

emp_2.first = 'Test'
emp_2.last = 'User'
emp_2.email = 'Test.User@company.com'
emp_2.pay = 60000

print(emp_1.email)
print(emp_2.email)
```

```text
Corey.Schafer@company.com
Test.User@company.com
```

This works, but setting every value by hand takes a lot of code. It is also easy to make a mistake, such as assigning one of `emp_2`'s values to `emp_1`. The class should set up this data when each employee is created instead.

### 4. Initialize each employee with `__init__`

Add the special `__init__` method to perform that setup automatically whenever an employee is created:

```python
class Employee:

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@company.com'
        self.pay = pay


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'User', 60000)

print(emp_1.email)
print(emp_2.email)
```

```text
Corey.Schafer@company.com
Test.User@company.com
```

Methods receive the instance automatically as their first argument. By convention, this argument is named `self`. The two calls provide the first name, last name, and pay, while Python provides each new instance as `self`. The email does not need another argument because it can be built from the first and last names.

For `emp_1`, `self.first = first` does the same job as the earlier manual assignment `emp_1.first = 'Corey'`, but now it happens during creation. An attribute name does not have to match its argument, so `self.fname = first` would also work. Matching names keep the relationship clear.

### 5. Display a full name manually

The class now stores the employee data, but it does not yet provide an action for displaying a full name. The first and last names can still be formatted outside the class:

```python
print('{} {}'.format(emp_1.first, emp_1.last))
```

```text
Corey Schafer
```

### 6. Add a `fullname` method

This produces the right result, but repeating the formatting each time would be unnecessary. Move it into a `fullname` method, replacing the specific `emp_1` references with `self` so the same method works with any employee instance:

```python
class Employee:

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@company.com'
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'User', 60000)

print(emp_1.fullname())
```

```text
Corey Schafer
```

Now the same method can display the second employee simply by changing the instance in the call:

```python
print(emp_2.fullname())
```

```text
Test User
```

The parentheses are important because they call the method. Without them, Python displays the method itself instead of the full name it returns:

```python
print(emp_1.fullname)
```

```text
<bound method Employee.fullname of <__main__.Employee object at 0x...>>
```

### 7. See why methods need `self`

The `fullname` method also needs a place to receive the instance. See what happens if `self` is omitted from its definition:

```python
def fullname():
    return '{} {}'.format(self.first, self.last)
```

Calling `emp_2.fullname()` now raises a `TypeError`. Although the call does not show an argument, Python automatically passes `emp_2`, while the method is defined to accept no arguments:

```text
TypeError: fullname() takes 0 positional arguments but 1 was given
```

Restore `def fullname(self):` so the method can receive that instance and use its first and last names.

### 8. Call the method through the instance or the class

One final comparison makes this automatic argument easier to see. These two calls do the same thing:

```python
print(emp_1.fullname())
print(Employee.fullname(emp_1))
```

```text
Corey Schafer
Corey Schafer
```

In the first call, the instance passes itself automatically. In the second, the method is called through `Employee`, so `emp_1` must be supplied explicitly as the instance that becomes `self`.

[Course home](../README.md) | [Next lesson: Class and Instance Variables](../2-Class-Instance-Variables/README.md)
