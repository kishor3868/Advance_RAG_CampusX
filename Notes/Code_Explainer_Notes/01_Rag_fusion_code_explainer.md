# Question 1: Why do we use _ (underscore) before a variable name?

## Example:

_log_path = "abc.log"
Answer

The underscore is a naming convention that tells other developers:

"This variable is intended for internal use only."

## Technical Name

This is called:

### Protected Member Convention

or

### Non-Public Naming Convention

Python does not enforce it.

It is simply a message to developers:

Please do not use this outside this module/class unless necessary.
Example
Public Variable
name = "Kishor"

Anyone can use this.

Internal Variable
_name = "Kishor"

Meaning:

This variable is for internal use.

# Question 2 Why do we use _ before function names?

Example:

def _calculate_salary():
    pass
Answer

The meaning is exactly the same:

This function is intended to be private or internal.

Example
def process_data():
    cleaned_data = _clean_data()
    return cleaned_data


def _clean_data():
    return "cleaned"

Here:

process_data()

is the public API.

while:

_clean_data()

is an internal helper function. 

# Question 3: What are the different underscore conventions in Python?
Single Leading Underscore
_name

Meaning:

Internal use only
Double Leading Underscore
__name

Meaning:

Name Mangling

Example:

class Test:
    def __init__(self):
        self.__salary = 100

Python internally converts it to:

_Test__salary

Used to avoid accidental overriding in inheritance.

Double Underscore Both Sides
__init__
__str__
__file__

These are called:

Magic Methods

or

Dunder Methods

Single Underscore Alone
_

Used for:

Ignoring values
Temporary variables
Last interpreter result

Example:

x, _, z = (1, 2, 3)

#  class SubQuerySchema(BaseModel):

    sub_queries: list[str] = Field(
        ...,
        description="List of sub-queries to be generated from the main query."
    )
## 1. Is this using Pydantic?
Yes.

This code is using the Pydantic library.

The imports are usually:

from pydantic import BaseModel, Field

Here:

BaseModel comes from Pydantic.
Field() also comes from Pydantic.

## 2. What is BaseModel?

BaseModel is the parent class provided by Pydantic.

It gives your class the ability to:

Validate data
Enforce types
Convert JSON to Python objects
Generate JSON Schema automatically

Without BaseModel, this would be a normal Python class.

Example
class Employee(BaseModel):
    name: str
    age: int

Now Pydantic ensures:

Employee(name="Kishor", age="25")

becomes:

Employee(name='Kishor', age=25)

Automatic type conversion happens.

## 3. What is Field()?

Field() is a helper function provided by Pydantic for adding:

Validation rules
Metadata
Descriptions
Examples
Constraints

Think of it as:

A way to provide extra information about a field.
Example
age: int = Field(
    gt=0,
    description="Employee age"
)

Meaning:

age must be greater than 0
description is added to schema/docs

## 4. What does this line mean?
sub_queries: list[str]

This means:

sub_queries should contain a list of strings.

Example valid input:

[
    "What is RAG?",
    "What is HNSW?",
    "What is IVF?"
]

Invalid:

[
    "What is RAG?",
    10,
    True
]

because:

10 is not a string
True is not a string

## 5. What does Field(...) mean?
Field(...)

The ... is called:

Ellipsis Object

In Pydantic it means:

This field is REQUIRED.

Equivalent meaning:

User must provide this value.
Example
class Employee(BaseModel):
    name: str = Field(...)

Valid:

Employee(name="Kishor")

Invalid:

Employee()

Error:

ValidationError:
name field required

## 6. Why not simply write this?
sub_queries: list[str]

Actually you can.

But then you lose:

descriptions
examples
validation rules
schema information

With Field:

sub_queries: list[str] = Field(
    ...,
    description="List of sub-queries"
)

Pydantic generates richer metadata.

## 7. What is the purpose of description=?
description="List of sub-queries to be generated from the main query."

This description is mainly used for:

API documentation
OpenAPI schema
FastAPI Swagger UI
LLM structured output generation
LangChain structured parsers

For example, an LLM may see:

sub_queries:
    List of sub-queries to be generated from the main query.

and understand what data it should produce.

## 8. What output does this model produce?

Example:

obj = SubQuerySchema(
    sub_queries=[
        "What is RAG?",
        "What is HNSW?"
    ]
)

Output object:

SubQuerySchema(
    sub_queries=[
        "What is RAG?",
        "What is HNSW?"
    ]
)

Dictionary form:

obj.model_dump()

returns:

{
    "sub_queries": [
        "What is RAG?",
        "What is HNSW?"
    ]
}

JSON form:

obj.model_dump_json()

returns:

{
  "sub_queries": [
    "What is RAG?",
    "What is HNSW?"
  ]
}
## 9. Why is this useful in LangChain and LLMs?

Suppose an LLM generates:

Bad output:

RAG
HNSW
IVF

This is unstructured.

Using Pydantic:

class SubQuerySchema(BaseModel):
    sub_queries: list[str]

LLM is forced to return:

{
    "sub_queries": [
        "What is RAG?",
        "What is HNSW?",
        "What is IVF?"
    ]
}

This makes parsing reliable.

## 10. Real Use Case in RAG Fusion

User asks:

Explain ANN indexing techniques.

LLM generates:

{
    "sub_queries": [
        "What is HNSW?",
        "What is IVF?",
        "What is PQ?",
        "What is Flat Index?"
    ]
}

Your application can now iterate:

for query in result.sub_queries:
    search(query)
Final Meaning of the Entire Code
class SubQuerySchema(BaseModel):

    sub_queries: list[str] = Field(
        ...,
        description="List of sub-queries to be generated from the main query."
    )

means:

Create a Pydantic model containing a required field called sub_queries, where the field must be a list of strings. The Field() function adds metadata and documentation, while ... specifies that the field is mandatory and cannot be omitted.

# 11 What is Runnable?

Runnable is a LangChain abstraction representing something that can be executed.

Examples:

PromptTemplate
LLM
Chain
LCEL Pipeline