---
name: value-object-pattern
description: Implements Value Objects in the Domain layer following Hexagonal Architecture and strict immutability principles.
when_to_use:
  - designing domain entities
  - encapsulating business rules
  - avoiding primitive obsession
  - modeling financial or structured data
  - validating domain-specific fields
---

# Value Object Pattern (Hexagonal + Immutable Domain)

You are implementing a Value Object in a Laravel 12 Hexagonal Architecture project.

This project uses:

- Laravel 12
- PHP 8.4
- Modular Monolith
- Hexagonal Architecture
- Octane (FrankenPHP)

Value Objects must be implemented inside the Domain layer and must be completely framework-independent.

---

# Core Definition

A Value Object:

- Has no identity.
- Is immutable.
- Represents a domain concept.
- Encapsulates validation rules.
- Is compared by value, not by reference.
- Contains no infrastructure logic.

Examples:

- Money
- Email
- ClaimId
- DateRange
- Percentage
- TenantId
- Status
- Quantity
- Currency

---

# Step 1 — Location

Value Objects must live in:

app/Modules/{ModuleName}/Domain/ValueObjects/

Never place Value Objects inside Infrastructure.
Never depend on Eloquent or Request.

---

# Step 2 — Immutability Rules

- Properties must be private.
- No setters allowed.
- Only constructor or named constructors.
- No mutable state.
- No public property access.

Use readonly properties when applicable (PHP 8.4).

---

# Step 3 — Constructor Validation

All validation must occur inside constructor or named constructors.

Invalid state must throw:

- InvalidArgumentException
- DomainException
- Custom Domain Exception

Never allow creation of invalid Value Objects.

---

# Step 4 — Named Constructors (Recommended)

Prefer named constructors for clarity.

Example patterns:

- fromString()
- fromInt()
- fromDatabase()
- fromFloat()
- approved()
- pending()

Named constructors must enforce invariants.

---

# Step 5 — Equality Comparison

Implement equals() method when relevant.

Comparison must be value-based.

Never rely on object identity.

Example rule:

Two Money objects are equal if amount and currency match.

---

# Step 6 — Serialization Rules

Value Objects may implement:

- toArray()
- toString()
- jsonSerialize()

But must not depend on framework classes.

If mapping to Eloquent:

Mapping must happen in Infrastructure layer.

Never expose Eloquent Model inside Value Object.

---

# Step 7 — Primitive Obsession Rule

Avoid using raw primitives for domain concepts.

Instead of:

string $email
int $amount
string $status

Use:

Email
Money
ClaimStatus

Value Objects must represent business meaning.

---

# Step 8 — Domain Safety

Value Objects must:

- Prevent invalid states.
- Centralize validation.
- Avoid duplicate validation across codebase.
- Be reusable.
- Be testable independently.

Never place validation logic in Controller if it belongs to domain.

---

# Step 9 — Octane Safety

Value Objects are inherently Octane-safe because:

- They are immutable.
- They do not store global state.
- They do not depend on container.
- They are pure PHP objects.

No special handling required.

---

# Step 10 — Database Mapping

Mapping rules:

Infrastructure layer must convert:

Eloquent Model → Value Object
Value Object → Eloquent field

Value Objects must not know about database.

Never use casts inside Domain.

Mapping belongs to Infrastructure adapters.

---

# Step 11 — Testing Requirements

Every Value Object must have unit tests covering:

- Valid creation.
- Invalid input rejection.
- Boundary conditions.
- Equality behavior.
- Serialization behavior (if applicable).

Value Objects must be tested in isolation.

---

# Prohibitions

- No Eloquent inside Value Object.
- No Request usage.
- No Cache usage.
- No Facades.
- No mutable setters.
- No silent invalid states.
- No static mutable properties.

---

# Output Requirements

When generating a Value Object:

1. Provide class definition.
2. Provide validation rules.
3. Provide named constructors if applicable.
4. Provide equals() method if relevant.
5. Provide serialization methods if needed.
6. Provide example usage in Entity.
7. Provide unit tests.

Always prioritize immutability and clarity.
Never compromise domain safety for convenience.