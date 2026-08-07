# Lesson 6: Property Decorators

[Previous: Lesson 5 - Special Methods](../5-SpecialMethods/README.md)

The property decorator lets a method be accessed like an attribute. This is
useful when a value needs to be calculated from other attributes while keeping
simple attribute-style access. A property can also define what happens when
that value is set or deleted.

## Step-by-Step Tutorial

### 1. Start with a Stored Email

This `Employee` class stores the email address when the object is created. The
email is built from the first and last names:

```python
class Employee:

    def __init__(self, first, last):
        self.first = first
        self.last = last
        self.email = '{}.{}@email.com'.format(first, last)

    def fullname(self):
        return '{} {}'.format(self.first, self.last)


emp_1 = Employee('John', 'Smith')

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname())
```

Output:

```text
John
John.Smith@email.com
John Smith
```

### 2. Change John to Jim

The stored email becomes a problem when one of those names changes:

```python
emp_1 = Employee('John', 'Smith')
emp_1.first = 'Jim'

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname())
```

Output:

```text
Jim
John.Smith@email.com
Jim Smith
```

The first name is now Jim, and the `fullname()` method also uses Jim because it
reads the current attributes each time it runs. The email still contains John
because that string was created and stored earlier in `__init__`. It does not
automatically change with `first`.

### 3. Use a Regular Email Method

One way to avoid stale email data is to remove the stored value and calculate
the address in a method. Like `fullname()`, the method will read the current
first and last names each time it runs:

```python
class Employee:

    def __init__(self, first, last):
        self.first = first
        self.last = last

    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)

    def fullname(self):
        return '{} {}'.format(self.first, self.last)
```

The email is now correct after the name change, but accessing it requires a
method call with parentheses:

```python
emp_1 = Employee('John', 'Smith')
emp_1.first = 'Jim'

print(emp_1.first)
print(emp_1.email())
print(emp_1.fullname())
```

Output:

```text
Jim
Jim.Smith@email.com
Jim Smith
```

### 4. Convert Email to a Property

The calculation should stay in a method, but the email should still be
accessed like an attribute. Add `@property` above the email method to combine
those two behaviors:

```python
class Employee:

    def __init__(self, first, last):
        self.first = first
        self.last = last

    @property
    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)

    def fullname(self):
        return '{} {}'.format(self.first, self.last)


emp_1 = Employee('John', 'Smith')
emp_1.first = 'Jim'

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname())
```

Output:

```text
Jim
Jim.Smith@email.com
Jim Smith
```

The email now uses the current name without requiring parentheses. Code can
continue to use `emp_1.email` as if it were a regular attribute.

### 5. Convert Full Name to a Property

The same decorator can make `fullname` a property. It remains a method inside
the class, but the parentheses are removed when accessing it:

```python
class Employee:

    def __init__(self, first, last):
        self.first = first
        self.last = last

    @property
    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)

    @property
    def fullname(self):
        return '{} {}'.format(self.first, self.last)


emp_1 = Employee('John', 'Smith')
emp_1.first = 'Jim'

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname)
```

Output:

```text
Jim
Jim.Smith@email.com
Jim Smith
```

### 6. Try to Set Full Name

At this point, `fullname` can be read as an attribute, but it cannot be
assigned a new value. With only the property getter, this assignment fails:

```python
emp_1.fullname = "Corey Schafer"
```

Output:

```text
AttributeError: can't set attribute
```

### 7. Add a Full Name Setter

To support assignment, add a setter to the `fullname` property. The decorator
and method use the property name, and the value being assigned is received as
`name`:

```python
@fullname.setter
def fullname(self, name):
    first, last = name.split(' ')
    self.first = first
    self.last = last
```

The setter splits the full name at the space, then stores the two parts in
`first` and `last`. Because the email property reads those current attributes,
it also reflects the new full name:

```python
emp_1 = Employee('John', 'Smith')
emp_1.fullname = "Corey Schafer"

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname)
```

Output:

```text
Corey
Corey.Schafer@email.com
Corey Schafer
```

### 8. Add a Full Name Deleter

The property can also define what happens when `fullname` is deleted. Add a
deleter using the same property name:

```python
@fullname.deleter
def fullname(self):
    print('Delete Name!')
    self.first = None
    self.last = None
```

```python
del emp_1.fullname
```

Output:

```text
Delete Name!
```

When `del emp_1.fullname` runs, Python calls this deleter. It prints the
message and sets the first and last names to `None`.

### 9. Complete Employee Example

The complete class keeps the email and full name as calculated properties,
uses a setter to update both stored name attributes, and uses a deleter to
clear them:

```python
class Employee:

    def __init__(self, first, last):
        self.first = first
        self.last = last

    @property
    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)

    @property
    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    @fullname.setter
    def fullname(self, name):
        first, last = name.split(' ')
        self.first = first
        self.last = last

    @fullname.deleter
    def fullname(self):
        print('Delete Name!')
        self.first = None
        self.last = None


emp_1 = Employee('John', 'Smith')
emp_1.fullname = "Corey Schafer"

print(emp_1.first)
print(emp_1.email)
print(emp_1.fullname)

del emp_1.fullname
```

Output:

```text
Corey
Corey.Schafer@email.com
Corey Schafer
Delete Name!
```

[Previous: Lesson 5 - Special Methods](../5-SpecialMethods/README.md)
