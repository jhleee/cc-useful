# Objective Principles

Objective principles used as the highest standard when evaluating feedback.

## 1. Clean Code Principles (Robert C. Martin)

### Meaningful Names
Names should reveal intent without comments.
- Use descriptive names over short cryptic ones
- Length proportional to scope (short scope = short name OK)

### Functions
- **Small**: ~20 lines ideal
- **Do one thing**: Single Responsibility
- **No side effects**: Pure when possible
- **Descriptive names**: Function name explains what it does

### Comments
- **Code > Comments**: Express intent in code, not comments
- Good comments: Legal, informative, warnings, TODOs
- Bad comments: Redundant, misleading, noise

### Error Handling
- Use exceptions, not error codes
- Separate error handling from business logic
- Don't return null

## 2. SOLID Principles

### SRP (Single Responsibility Principle)
A class/module should have one reason to change.
- ❌ Class handling DB + email + logging
- ✅ Separate classes for each responsibility

### OCP (Open-Closed Principle)
Open for extension, closed for modification.
- ❌ Adding `if` statements for new types
- ✅ Using interfaces/inheritance for extensibility

### LSP (Liskov Substitution Principle)
Subtypes must be substitutable for base types.
- ❌ `Penguin extends Bird` where `Penguin.fly()` throws error
- ✅ Proper abstraction that doesn't violate expectations

### ISP (Interface Segregation Principle)
Clients shouldn't depend on interfaces they don't use.
- ❌ Fat interfaces forcing unused method implementations
- ✅ Small, focused interfaces

### DIP (Dependency Inversion Principle)
Depend on abstractions, not concretions.
- ❌ `new MySQLDatabase()` hardcoded
- ✅ Inject `Database` interface

## 3. Architecture Patterns

### Layered Architecture
Separate concerns by layer:
- **Presentation**: Controllers, UI
- **Business**: Services, domain logic
- **Persistence**: Repositories, DB

Dependencies flow downward only.

### Separation of Concerns
Each module handles one concern independently.
- ❌ Validation + calculation + DB + email in one function
- ✅ Separate modules for each concern

### DRY (Don't Repeat Yourself)
Avoid code duplication.

**Exception**: Different domains can have duplicate code if:
- They may evolve differently
- Domain independence > code deduplication
- Example: `UserValidator.validateEmail()` vs `NewsletterValidator.validateEmail()`

## 4. Best Practices

### YAGNI (You Aren't Gonna Need It)
Don't build features you don't need yet.
- ❌ Adding fields/methods "for future use"
- ✅ Build only what's needed now

### KISS (Keep It Simple, Stupid)
Simplest solution that works.
- ❌ Abstract factory for creating simple objects
- ✅ Direct instantiation when appropriate

### Composition over Inheritance
Prefer composition to deep inheritance hierarchies.
- ❌ `SwimmingFlyingAnimal extends FlyingAnimal extends Animal`
- ✅ Interfaces: `Flyable`, `Swimmable`, `Eatable`

## 5. Decision Framework

When evaluating feedback:

1. **Does it align with objective principles?**
   - Clean Code, SOLID, Architecture patterns

2. **Does it improve code measurably?**
   - Readability, maintainability, testability

3. **Is it contextually appropriate?**
   - Project phase (prototype vs production)
   - Scale (small script vs large system)
   - Team conventions

### Priority when principles conflict:

```
1. Working code > Perfect design
2. Readability > Performance (unless bottleneck)
3. Simplicity > Extensibility (build what you need)
4. Practicality > Theoretical perfection
```

## References

- Clean Code - Robert C. Martin
- Refactoring - Martin Fowler
- Design Patterns - Gang of Four
- SOLID Principles - Robert C. Martin

---

**Last Updated**: 2026-01-25
**Version**: 3.0.0
