# Lesson 4: Inheritance and Subclasses

[Previous: Lesson 3](../3-Class-Static-Methods/README.md) | [Next: Lesson 5](../5-SpecialMethods/)

The `Employee` class already handles names, email addresses, pay, and raises.
Developers and managers need all of that behavior, plus details of their own.
Inheritance lets each new class reuse `Employee`, then override or add only
what makes that type of employee different.

## Step-by-Step Tutorial

### 1. Begin with `Employee`

Begin with the existing `Employee` class:

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


dev_1 = Employee('Corey', 'Schafer', 50000)
dev_2 = Employee('Test', 'Employee', 60000)

print(dev_1.email)
print(dev_2.email)
```

```text
Corey.Schafer@email.com
Test.Employee@email.com
```

The two objects behave as expected, but their names suggest that they are
developers. Creating a separate `Developer` class will make that distinction
without copying the name, email, pay, and raise behavior already provided by
`Employee`. Managers will later reuse the same common behavior.

### 2. Add an empty `Developer` subclass

Create `Developer` and place its parent class, `Employee`, in parentheses. It
does not need any code of its own yet:

```python
class Developer(Employee):
    pass
```

Now create the same two people as developers:

```python
dev_1 = Developer('Corey', 'Schafer', 50000)
dev_2 = Developer('Test', 'Employee', 60000)

print(dev_1.email)
print(dev_2.email)
```

```text
Corey.Schafer@email.com
Test.Employee@email.com
```

The email addresses are still created correctly even though `Developer` is
empty. When Python does not find `__init__` in `Developer`, it follows the
inheritance chain and finds `Employee.__init__`. The same process gives
`Developer` access to `fullname()`, `apply_raise()`, and `raise_amt`.

Here, `Employee` is the parent or base class, and `Developer` is the subclass.

### 3. Inspect the method resolution order with `help()`

Use `help()` to make that search path visible:

```python
print(help(Developer))
```

Near the top, the output shows the method resolution order:

```text
Developer
 |  Employee
 |  builtins.object
```

Python searches `Developer` first, then `Employee`, and finally `object`, the
base of every Python class. The rest of the output shows the inherited
`__init__`, `apply_raise()`, `fullname()`, and `raise_amt`. Remove the `help()`
call before continuing.

### 4. Test and override the raise amount

With the inherited behavior established, observe what happens when the first
developer receives the inherited 4 percent raise:

```python
print(dev_1.pay)
dev_1.apply_raise()
print(dev_1.pay)
```

```text
50000
52000
```

Suppose developers should instead receive a 10 percent raise. Override only
the class attribute in `Developer`:

```python
class Developer(Employee):
    raise_amt = 1.10
```

Repeat the same test with a newly created `dev_1`:

```text
50000
55000
```

The inherited `apply_raise()` still supplies the behavior, but its
`self.raise_amt` lookup now finds `Developer.raise_amt`. The subclass changes
the value without rewriting the method.

To confirm that the parent remains unchanged, temporarily create `dev_1` as an
`Employee` again:

```python
dev_1 = Employee('Corey', 'Schafer', 50000)
```

```text
50000
52000
```

The raise returns to 4 percent because changing the subclass attribute did not
change `Employee.raise_amt`. Restore `dev_1` as a `Developer` before adding
developer-specific data.

### 5. Give developers a programming language

A developer also needs a main programming language, but `Employee.__init__`
only accepts a first name, last name, and pay. Give `Developer` its own
initializer while allowing `Employee` to keep handling those shared values:

```python
class Developer(Employee):
    raise_amt = 1.10

    def __init__(self, first, last, pay, prog_lang):
        super().__init__(first, last, pay)
        self.prog_lang = prog_lang
```

`super().__init__(first, last, pay)` calls the parent initializer. The same
initializer can also be called explicitly:

```python
Employee.__init__(self, first, last, pay)
```

Both forms work here. Continue with `super()` and let `Developer` set only its
new `prog_lang` attribute.

Now pass the programming languages and inspect the first developer:

```python
dev_1 = Developer('Corey', 'Schafer', 50000, 'Python')
dev_2 = Developer('Test', 'Employee', 60000, 'Java')

print(dev_1.email)
print(dev_1.prog_lang)
```

```text
Corey.Schafer@email.com
Python
```

### 6. Add the `Manager` subclass

With `Developer` specialized, evolve the model again by adding `Manager`.
Like `Developer`, it inherits the common employee data, but its additional
state is a list of employees to supervise:

```python
class Manager(Employee):

    def __init__(self, first, last, pay, employees=None):
        super().__init__(first, last, pay)
        if employees is None:
            self.employees = []
        else:
            self.employees = employees

    def add_emp(self, emp):
        if emp not in self.employees:
            self.employees.append(emp)

    def remove_emp(self, emp):
        if emp in self.employees:
            self.employees.remove(emp)

    def print_emps(self):
        for emp in self.employees:
            print('-->', emp.fullname())
```

The `employees` argument defaults to `None` rather than a mutable list. If no
list is supplied, the initializer creates a new empty list; otherwise, it uses
the supplied list. The three manager methods then add an employee if absent,
remove an employee if present, and print each employee's inherited full name.

### 7. Test the manager methods in sequence

Now observe each manager behavior in sequence. Create Sue Smith with Corey
Schafer in her employee list and `90000` for her pay, then check the email
inherited from `Employee`:

```python
mgr_1 = Manager('Sue', 'Smith', 90000, [dev_1])

print(mgr_1.email)
```

```text
Sue.Smith@email.com
```

First, print the initial employee list:

```python
mgr_1.print_emps()
```

```text
--> Corey Schafer
```

Next, add the second developer and print again:

```python
mgr_1.add_emp(dev_2)
mgr_1.print_emps()
```

```text
--> Corey Schafer
--> Test Employee
```

Finally, remove the first developer and print again:

```python
mgr_1.remove_emp(dev_1)
mgr_1.print_emps()
```

```text
--> Test Employee
```

### 8. Check objects with `isinstance()`

The manager now combines inherited employee behavior with manager-specific
behavior. Use `isinstance()` to inspect those relationships for `mgr_1`:

```python
print(isinstance(mgr_1, Manager))
print(isinstance(mgr_1, Employee))
print(isinstance(mgr_1, Developer))
```

```text
True
True
False
```

`mgr_1` is a `Manager` and, through inheritance, an `Employee`. It is not a
`Developer`; the two subclasses share a parent but do not inherit from each
other.

### 9. Check classes with `issubclass()`

The same relationships can be checked at the class level with
`issubclass()`:

```python
print(issubclass(Developer, Employee))
print(issubclass(Manager, Employee))
print(issubclass(Manager, Developer))
```

```text
True
True
False
```

### 10. Apply inheritance to HTTP exceptions

The same pattern appears in HTTP exceptions. An HTTP exception base class can
contain the shared exception behavior, while specialized exceptions such as
`BadRequest` inherit all of that code. The `BadRequest` class then needs to
change only its return code and description instead of rewriting the parent
behavior.

[Previous: Lesson 3](../3-Class-Static-Methods/README.md) | [Next: Lesson 5](../5-SpecialMethods/)
