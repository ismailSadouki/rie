![[260812_22h04m45s_screenshot.png]]![[260812_21h58m22s_screenshot.png]]![[260812_21h37m34s_screenshot.png]]
By the end of this chapter, you will have a fully typed GenAI service that is less
prone to bugs when dealing with changes, bad user inputs, and inconsistent
model responses.

# Introduction to Type Safety
. In Python, you can use types to
check the usage of variables across a codebase, in particular if the codebase
grows in complexity and size. Type checking tools (e.g., mypy) can then use
these types to catch incorrect variable assignments or operations.

Using typing, you can catch errors more quickly. Without typing, you must
rely on tests, and insufficient test coverage can lead to production crashes
due to mistakes. At best, you remain unaware of minor issues and nuances
affecting the user. At worst, you risk data corruption or incur massive server
costs, only discovering the damage when it is too late.

# Implementing Type Safe


![[260812_21h33m50s_screenshot.png]]
![[260812_21h34m04s_screenshot.png]]

# Using Annotated
In Example 4-2, you can use Annotated instead of type aliases. Annotated is a
feature of the typing module—introduced in Python 3.9—and is similar to type
aliases for reusing types, but it allows you to also define metadata for your
types.
The metadata doesn’t affect the type checkers but is useful for code
documentation, analysis, and runtime inspections.

![[260812_21h37m34s_screenshot.png]]
The FastAPI documentation recommends the use of Annotated instead of type
aliases for reusability, for enhanced type checks in the code editor, and for
catching issues during runtime.


# Dataclasses

If you need custom data structures, you can use dataclasses to organize, store, and transfer data across your application.

They can help with avoiding code “smells” such as function parameter bloat,
where a function is hard to use because it requires more than a handful of
parameters. Having a dataclass allows you to organize your data in a custom-
defined structure and pass it as a single item to functions that require data from
different places.


![[260812_21h58m22s_screenshot.png]]
The primary benefit of using dataclasses in Example 4-4 was to group related
parameters to simplify the function signature. In other scenarios, you may use
dataclasses to:
- Eliminate code duplication
- Shrink down code bloat (large classes or functions)
- Refactor data clumps (variables that are commonly used together)
- Prevent inadvertent data mutation
- Promote data organization
- Promote encapsulation
- Enforce data validation

# Pydantic Models
Pydantic is the most widely used data validation library with support for custom
validators and serializers. Pydantic’s core logic is controlled by type annotations
in Python and can emit data in JSON format, allowing for seamless integration
with any other tools.

 When you create Pydantic models, a set of initialization hooks are called that add data validation, serialization, and JSON schema generation features to the models that vanilla dataclasses lack.

#### How to Use Pydantic
install in ur project
```
pip install pydantic
```
Pydantic at its core implements a BaseModel, which is the primary method for
defining models. Models are simply classes that inherit from BaseModel and
define fields as annotated attributes using type hints. Any models can then be
used as schemas to validate your data.

Aside from grouping data, Pydantic models let you specify the request and
response requirements of your service endpoints and validate incoming untrusted
data from external sources. You can also go as far as filter your LLM outputs
using Pydantic models (and validators, which you will learn more about shortly)


![[260812_22h04m45s_screenshot.png]]

#### Compound Pydantic Models
With Pydantic models, you can declare data schemas, which define data
structures supported in the operations of your service. Additionally, you can also
use inheritance for building compound models, as shown in Example 4-6


![[260812_22h10m20s_screenshot.png]]

With the models shown in Example 4-6, you have schemas needed to define the
requirements of your text and image generation endpoints


#### Field Constraints and Validators
Aside from support for standard types, Pydantic also ships with constrained
types such as EmailStr, PositiveInt, UUID4, AnyHttpUrl, and more that can
perform data validation out of the box during model initialization for common
data formats.

To define more custom and complex field constraints on top of Pydantic-
constrained types, you can use the Field function from Pydantic with the
Annotated type to introduce validation constraints such as a valid input range

Example 4-7 replaces the standard type hints in Example 4-6 with constrained
types and Field functions to implement stricter data requirements for your
endpoints based on model constraints.


![[260812_22h54m59s_screenshot.png]]

With the models defined in Example 4-7, you can now perform validation on
incoming or outgoing data to match the data requirements you have. In such
cases, FastAPI will leverage Pydantic to automatically return error responses
when data validation checks fail during a request runtime.

#### Custom Field and Model Validators
Another excellent feature of Pydantic for performing data validation checks is
custom field validators. Example 4-9 shows how both types of custom validators
can be implemented on the ImageModelRequest.


![[260812_22h58m44s_screenshot.png]]
![[260812_22h58m56s_screenshot.png]]
![[260812_23h07m09s_screenshot.png]]


#### Computed Fields
Similar to dataclasses, Pydantic also allows you to implement methods to
compute fields derived from other fields.
You can use the @computed_field decorator to implement a computed field for
calculating count of tokens and cost, as shown in Example 4-10.

![[260812_23h08m42s_screenshot.png]]


Computed fields are useful for encapsulating any field computation logic inside
your Pydantic models to keep code organized. Bear in mind that computed fields
are only accessible when you convert a Pydantic model to a dictionary using
.model_dump() or via serialization when a FastAPI API handler returns a
response.

#### Model Export and Serialization

As Pydantic models can serialize to JSONs, the models you defined in
Example 4-7 can also be dumped into (or be loaded from) JSON strings or
Python dictionaries while maintaining any compound schemas, as shown in
Example 4-11.

![[260812_23h11m47s_screenshot 1.png]]

####  Parsing Environment Variables with Pydantic
Alongside the BaseModel, Pydantic also implements a Base class for parsing
settings and secrets from files. This feature is provided in an optional Pydantic
package called pydantic-settings, which you can install as a dependency:
```
pip install pydantic-settings
```
The BaseSettings class provides optional Pydantic features for loading a
settings or config class from environment variables or secret files. Using this
feature, the settings values can be set in code or overridden by environment
variables
This is useful in production where you don’t want to expose secrets inside the
code or the container environment.


<!--⚠️Imgur upload failed, check dev console-->![[260812_23h17m06s_screenshot.png]]
<!--⚠️Imgur upload failed, check dev console-->![[260812_23h17m16s_screenshot.png]]



**NOTE**
You can switch environment files when using the _env_file argument:
```

test_settings = AppSettings(_env_file="test.env")
```

# Dataclasses or Pydantic Models in FastAPI
Even though dataclasses support serialization of only the common types (e.g.,
int, str, list, etc.) and won’t perform field validation at runtime, FastAPI can
still work with both Pydantic models and Python’s dataclasses. For field
validation and additional features, you should use Pydantic models. Example 4-
13 shows how dataclasses can be used in FastAPI route handlers.

![[260812_23h20m24s_screenshot.png]]











---
what is annotated, Literal,  Pydantic model