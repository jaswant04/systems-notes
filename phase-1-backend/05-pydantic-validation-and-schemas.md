# Pydantic — Validation and Schemas in FastAPI

## What this concept is about
This note covers how Pydantic is used in FastAPI to validate, parse, and structure data using schemas. It focuses on the practical subset of Pydantic required to build safe, production-ready APIs.

---

## Why Pydantic exists
APIs receive untrusted input:
- missing fields
- wrong data types
- invalid formats
- extra or unexpected data

Without a schema layer, validation logic spreads across endpoints and becomes repetitive and error-prone.

Pydantic solves this by:
- defining data contracts once
- validating data automatically
- rejecting invalid input before endpoint logic runs

---

## What Pydantic is
Pydantic is a data validation and parsing library based on Python type hints.

In FastAPI:
- request bodies are validated using Pydantic models
- responses can be validated and shaped using Pydantic models
- API documentation is generated from schemas

FastAPI delegates all data validation to Pydantic.

---

## BaseModel and schema creation

    from pydantic import BaseModel

    class User(BaseModel):
        name: str
        age: int

This defines a schema where:
- `name` must be a string
- `age` must be an integer
- both fields are required

When data is passed:
- types are validated
- values are converted if possible
- invalid data raises a validation error

---

## Validation lifecycle (important)

When a request body is received:
1. Raw JSON is parsed
2. Data is passed to Pydantic
3. Type conversion happens
4. Field constraints are checked
5. Custom validators run
6. Object is created if valid

If any step fails:
- the endpoint function is NOT executed
- FastAPI returns a 422 error

---

## Required fields, optional fields, and defaults

### Required field

    name: str

- key must exist
- value must not be null

### Nullable but required field

    name: str | None

- key must exist
- value may be null

### Optional field with default

    name: str | None = None

- key may be missing
- value may be null
- default is used if key is missing

### Default value

    role: str = "user"

- key may be missing
- default value is assigned
- null is NOT allowed unless explicitly specified

---

## Field constraints using Field()

    from pydantic import Field

    class User(BaseModel):
        name: str = Field(min_length=2, max_length=50)
        age: int = Field(ge=18, le=100)

Field constraints:
- enforce limits
- improve error messages
- keep validation declarative

---

## Regex validation

Regex is used for format validation.

    class User(BaseModel):
        username: str = Field(pattern="^[a-zA-Z0-9_]+$")

Regex is applied only if the value exists.
If the pattern fails, validation error is raised.

Use regex for:
- usernames
- phone numbers
- IDs
- simple format checks

---

## Custom validation using field_validator

    from pydantic import field_validator

    class User(BaseModel):
        email: str

        @field_validator("email")
        def validate_email(cls, value):
            if "@" not in value:
                raise ValueError("invalid email")
            return value

Important rules:
- validator receives already-parsed value
- must return a value
- raising ValueError fails validation

---

## Validation order

1. Type conversion
2. Field constraints
3. field_validator
4. Object creation

Validators do not receive raw input.

---

## Cross-field validation

    class User(BaseModel):
        password: str
        confirm_password: str

        @field_validator("confirm_password")
        def passwords_match(cls, value, info):
            if value != info.data.get("password"):
                raise ValueError("passwords do not match")
            return value

Used when validation depends on multiple fields.

---

## Nested models

    class Address(BaseModel):
        city: str
        pincode: str

    class User(BaseModel):
        name: str
        address: Address

Nested models:
- are validated independently
- produce structured Python objects
- fail the entire request if invalid

---

## Lists and complex structures

    class Item(BaseModel):
        id: int
        quantity: int

    class Order(BaseModel):
        items: list[Item]

Each element in the list is validated individually.

---

## Schema reuse patterns

Do not reuse one schema everywhere.

Preferred pattern:

    class UserBase(BaseModel):
        name: str
        email: str

    class UserCreate(UserBase):
        password: str

    class UserUpdate(BaseModel):
        name: str | None = None
        email: str | None = None

    class UserResponse(UserBase):
        id: int

This prevents:
- leaking sensitive fields
- accidental over-validation
- inconsistent API contracts

---

## Response models

    @app.get("/users/{id}", response_model=UserResponse)
    def get_user(id: int):
        return {
            "id": id,
            "name": "A",
            "email": "a@x.com",
            "password": "secret"
        }

FastAPI:
- filters extra fields
- enforces response structure
- documents response shape

---

## Annotated for reusable constraints

    from typing import Annotated

    Email = Annotated[
        str,
        Field(pattern="^[^@]+@[^@]+\.[^@]+$")
    ]

    class User(BaseModel):
        email: Email

Annotated separates:
- type definition
- validation metadata

This improves reuse and clarity.

---

## default_factory

Mutable or runtime-generated defaults must use default_factory.

    class User(BaseModel):
        tags: list[str] = Field(default_factory=list)

Without default_factory:
- mutable defaults are shared
- subtle bugs occur

Common uses:
- lists and dicts
- timestamps
- UUIDs

---

## What NOT to put in Pydantic models

Avoid:
- database access
- API calls
- business rules
- side effects

Schemas should be:
- fast
- deterministic
- side-effect free

---

## Scope of this note
This note intentionally covers the **practical subset** of Pydantic required for backend API development.

Advanced topics not covered here:
- performance tuning
- model config internals
- custom error handling
- deep serialization control

These can be learned later if needed.

---

## Key takeaway
Pydantic provides executable schemas that validate, parse, and protect data before it reaches application logic, making FastAPI APIs safer, cleaner, and easier to maintain.
