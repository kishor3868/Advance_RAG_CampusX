# @classmethod
def from_retrievers(cls,
                    base_retrievers: list[BaseRetriever],
                    weights: list[float] | None = None,
                    k: int = 5):
Corrected Question

# Explain @classmethod from start to end using very simple examples and explain every related term as if teaching a 10th class student.

Step 1: First Understand Classes and Objects

Suppose we have a class:

class Student:
    pass

Think of a class as a blueprint.

Example:

House Blueprint  → Class
Actual House     → Object

Similarly:

Student Class    → Blueprint
Rahul Object     → Actual Student
Priya Object     → Actual Student
Step 2: What is self?

Consider:

class Student:

    def __init__(self, name):
        self.name = name

    def display(self):
        print(self.name)

Create object:

s1 = Student("Rahul")
s2 = Student("Priya")

Internally Python does:

Student.display(s1)
Student.display(s2)

So:

For first call:

self → s1

For second call:

self → s2

Therefore:

self refers to the current object.

Step 3: Problem with self

Suppose we want information about the class itself, not a particular student.

Example:

School Name = DAV Public School

This belongs to all students.

It doesn't belong to Rahul or Priya individually.

Example:

class Student:

    school_name = "DAV Public School"

Now:

print(Student.school_name)

Output:

DAV Public School
Step 4: Enter @classmethod

Suppose we write:

class Student:

    school_name = "DAV Public School"

    @classmethod
    def get_school(cls):
        print(cls.school_name)

Call:

Student.get_school()

Output:

DAV Public School
What is cls?

When Python executes:

Student.get_school()

Internally it becomes:

Student.get_school(Student)

Therefore:

cls → Student

So:

cls.school_name

means:

Student.school_name
Difference Between self and cls
Feature	self	cls
Refers to	Object	Class
Receives	Instance	Class
Used in	Normal Methods	Class Methods
Example	Rahul object	Student class
Visual Representation
Student Class
│
├── Rahul Object
├── Priya Object
└── Aman Object
self
self → Rahul
cls
cls → Student Class
Step 5: Why Do We Need @classmethod?

The biggest reason:

To create objects in different ways.

Example Without Class Method

Suppose constructor is:

class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age

Create object:

s1 = Student("Rahul", 15)

Now suppose data comes as:

data = "Rahul-15"

We cannot do:

Student(data)

because constructor needs:

name
age

separately.

Solution Using @classmethod
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def from_string(cls, student_data):

        name, age = student_data.split("-")

        return cls(name, int(age))

Now:

s1 = Student.from_string("Rahul-15")

works perfectly.

Internally What Happens?

Python executes:

return cls(name, int(age))

Since:

cls → Student

it becomes:

return Student(name, int(age))

which becomes:

return Student("Rahul",15)
Why Not Simply Write This?
return Student(name, int(age))

Good question.

Because inheritance exists.

Step 6: Why cls() Instead of Class Name?

Suppose:

class Student:

    def __init__(self,name,age):
        self.name=name
        self.age=age

    @classmethod
    def from_string(cls,data):
        name, age = data.split("-")
        return cls(name,int(age))

Now another class comes:

class SchoolStudent(Student):
    pass

Call:

obj = SchoolStudent.from_string("Rahul-15")

Since:

cls → SchoolStudent

Python creates:

SchoolStudent("Rahul",15)

instead of:

Student("Rahul",15)

This makes inheritance work automatically.

Technical Name of This Pattern

This pattern is called:

Factory Method Pattern

or

Alternative Constructor

Why "Alternative Constructor"?

Main constructor:

Student(name, age)

Alternative constructor:

Student.from_string()

Another alternative constructor:

Student.from_json()

Another one:

Student.from_database()

All create Student objects but from different input formats.

Real World Example

Imagine ordering pizza.

Normal constructor:

Pizza(size, cheese, toppings)

Alternative constructors:

Pizza.from_veg_combo()
Pizza.from_family_pack()
Pizza.from_party_pack()

Different ways to create the same pizza object.

Common Examples in Python
From Dictionary
@classmethod
def from_dict(cls, data):
    return cls(
        data["name"],
        data["age"]
    )
From JSON
@classmethod
def from_json(cls, data):
    parsed = json.loads(data)
    return cls(**parsed)
From Database
@classmethod
def from_database(cls, user_id):
    data = database.fetch(user_id)
    return cls(**data)
Is @classmethod Mandatory?

No.

You use it when:

Method works with class-level data.
You want alternative constructors.
You want inheritance-friendly object creation.
When NOT to Use @classmethod

Do not use it when the method needs object-specific data.

Example:

def calculate_marks(self):

This depends on a specific student.

So:

Use self

not:

Use cls
Final One-Line Definition

@classmethod is a decorator that makes a method receive the class (cls) instead of the object (self) as its first argument and is commonly used to create alternative constructors and factory methods.

# Corrected Question

# When we create an object using a class method like Student.from_string(data), does the constructor __init__() get called or not? Are the instance variables (self.name, self.age) created? Is memory allocated for them? What exactly happens internally?

Consider:

class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def from_string(cls, data):
        name, age = data.split("-")
        return cls(name, int(age))

and:

student = Student.from_string("Rahul-15")
Short Answer

✅ Yes, the constructor __init__() gets called.

✅ Yes, self.name and self.age are created.

✅ Yes, memory is allocated for the object and its variables.

## What Actually Happens Internally?

When Python executes:

Student.from_string("Rahul-15")

the following steps happen:

### Step 1: Call the Class Method

Python executes:

cls = Student

So inside the method:

name, age = data.split("-")

becomes:

name = "Rahul"
age = "15"
### Step 2: Execute This Line
return cls(name, int(age))

Since:

cls = Student

this becomes:

return Student("Rahul", 15)

Notice carefully:

This is exactly the same as writing:

student = Student("Rahul", 15)
### Step 3: Python Creates the Object

Python internally performs:

1. Allocate memory for Student object
2. Create empty Student instance
3. Call __init__()
4. Return object reference
5. 
### Step 4: Constructor Runs

Python automatically executes:

__init__(self, "Rahul", 15)

where:

self → newly created Student object

### Step 5: Instance Variables Are Created

Now these lines execute:

self.name = name
self.age = age

which become:

student.name = "Rahul"
student.age = 15
Memory Representation

Before constructor:

Student Object
┌─────────────┐
│ Empty Object│
└─────────────┘

After constructor:

Student Object
┌─────────────────┐
│ name = Rahul    │
│ age  = 15       │
└─────────────────┘
Visual Flow
Student.from_string("Rahul-15")
            ↓
cls = Student
            ↓
return Student("Rahul",15)
            ↓
Memory Allocated
            ↓
__init__() Called
            ↓
self.name = "Rahul"
self.age = 15
            ↓
Return Object
Important Point

The class method itself does not create the object.

This line creates the object:

cls(name, int(age))

because:

cls(...)

is equivalent to:

Student(...)

and calling a class like a function automatically creates an object and invokes __init__().

When Would __init__() NOT Be Called?

Only if someone explicitly bypasses it:

obj = Student.__new__(Student)

Here:

Memory Allocated → YES
__init__() Called → NO

The object exists but:

obj.name

would raise:

AttributeError

because self.name was never created.

Real-Life Analogy

Think of:

Student("Rahul",15)

as ordering a new mobile phone.

Python does:

1. Manufacture phone body (memory allocation)
2. Install software (__init__)
3. Configure settings (self.name, self.age)
4. Hand over phone to customer

from_string() is simply another way of placing the order.

Instead of saying:

Give me a phone with:
name=Rahul
age=15

you say:

Here is a string:
"Rahul-15"
Please build the object from this.

The actual manufacturing process (__init__) still happens exactly the same way.