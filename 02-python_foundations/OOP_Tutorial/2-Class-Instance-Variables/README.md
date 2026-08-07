# Lesson 2: Class and Instance Variables

[Course home](../README.md) | [Previous lesson: Classes and Instances](../1-FirstClasses/README.md) | [Next lesson: Class Methods and Static Methods](../3-Class-Static-Methods/README.md)

## Step-by-Step Tutorial

### 1. Begin with instance variables

Begin with the `Employee` class setting a name, email address, and pay for each employee:

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
```

These are instance variables because their values can be unique to each employee. A class variable serves a different purpose: it holds a value shared among all instances of the class.

### 2. Hard-code a four-percent raise

Suppose the company gives every employee the same annual raise. The amount may change from year to year, but it applies to every employee. Before making it a shared class variable, add an `apply_raise` method with a four-percent raise hard-coded:

```python
class Employee:

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@company.com'
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * 1.04)


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'User', 60000)

print(emp_1.pay)
emp_1.apply_raise()
print(emp_1.pay)
```

```text
50000
52000
```

The pay changes from `50000` to `52000`, and `int(...)` keeps the result as a whole number. This works, but `1.04` is hidden inside the method. The amount cannot be accessed as an attribute, and changing it would be inconvenient if the same value appeared in several places.

### 3. Add the `raise_amt` class variable

Because the raise amount should be shared, move it to the top of the class as `raise_amt`. Then replace the hard-coded value in `apply_raise` with that class variable:

```python
class Employee:

    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@company.com'
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amt)
```

The class variable cannot be used by its bare name inside the method:

```python
self.pay = int(self.pay * raise_amt)
```

```text
NameError: name 'raise_amt' is not defined
```

Access a class variable through the class, as `Employee.raise_amt`, or through an instance, as `self.raise_amt`. Both forms work here. Keep `self.raise_amt` in the method; the reason for that choice becomes clear after changing the value for one instance.

### 4. Access `raise_amt` through the class and instances

Now print the shared value through the class and through each instance:

```python
emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'User', 60000)

print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)
```

```text
1.04
1.04
1.04
```

All three expressions return `1.04`, even though `raise_amt` was defined only on the class. When Python looks for an attribute on an instance, it checks the instance first. If the attribute is not there, it checks the class and then classes it inherits from. Neither employee has its own `raise_amt`, so both find `Employee.raise_amt`.

### 5. Inspect the namespaces with `__dict__`

Inspect the namespaces to see that lookup more directly. First, print the namespace of `emp_1`:

```python
print(emp_1.__dict__)
```

```text
{'first': 'Corey', 'last': 'Schafer', 'email': 'Corey.Schafer@company.com', 'pay': 50000}
```

The instance contains its own name, email address, and pay, but no `raise_amt`. Next, print the class namespace:

```python
print(Employee.__dict__)
```

Among its other contents, the output includes the class variable:

```text
'raise_amt': 1.04
```

This confirms why `emp_1.raise_amt` works: Python does not find the name on `emp_1`, so it finds the value in the class namespace instead.

### 6. Change the class variable

Now change the shared raise amount from `1.04` to `1.05` by assigning it through the class:

```python
Employee.raise_amt = 1.05

print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)
```

```text
1.05
1.05
1.05
```

The class and both instances now return `1.05`. Because neither instance has its own `raise_amt`, they both continue to use the value changed on `Employee`.

### 7. Override the value for one instance

Changing the class affects every employee that uses its value. To see what changes when the assignment is made through an instance, set the class value back to `1.04` and give only `emp_1` a value of `1.05`:

```python
Employee.raise_amt = 1.04
emp_1.raise_amt = 1.05

print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)
```

```text
1.04
1.05
1.04
```

This time, only `emp_1` returns `1.05`. The assignment creates a `raise_amt` attribute on that instance; it does not change `Employee` or `emp_2`.

```python
print(emp_1.__dict__)
```

```text
{'first': 'Corey', 'last': 'Schafer', 'email': 'Corey.Schafer@company.com', 'pay': 50000, 'raise_amt': 1.05}
```

The namespace now shows the new attribute on `emp_1`. Python finds this value before it needs to check the class, while `emp_2` still has no instance value and falls back to `Employee.raise_amt`.

This is why `apply_raise` uses `self.raise_amt`: it can use an instance's own raise amount when one exists, or the class value when it does not:

```python
def apply_raise(self):
    self.pay = int(self.pay * self.raise_amt)
```

### 8. Count employees with a class variable

The raise amount can be overridden for one employee, but a count of all employees should never differ from one instance to another. Add a second class variable, `num_of_emps`, starting at zero. Then increment it in `__init__`, which runs whenever a new employee is created:

```python
class Employee:

    num_of_emps = 0
    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@company.com'
        self.pay = pay

        Employee.num_of_emps += 1

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amt)


print(Employee.num_of_emps)

emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'User', 60000)

print(Employee.num_of_emps)
```

```text
0
2
```

The first print occurs before any employees are created, so the count is `0`. Creating `emp_1` and `emp_2` runs `__init__` twice and increments the shared count each time, so the second print returns `2`.

The counter uses `Employee.num_of_emps` instead of `self.num_of_emps` because the total belongs to the class and should not be overridden for any one instance.

[Course home](../README.md) | [Previous lesson: Classes and Instances](../1-FirstClasses/README.md) | [Next lesson: Class Methods and Static Methods](../3-Class-Static-Methods/README.md)
