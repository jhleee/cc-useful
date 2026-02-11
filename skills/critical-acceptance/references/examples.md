# Feedback Evaluation Examples

Detailed examples for Accept, Reject, and Negotiate decisions.

## Response Structure

```markdown
## Decision: [ACCEPT / REJECT / NEGOTIATE]

### Feedback
[Summary]

### Code Analysis
[Current state]

### Evaluation
[Assessment based on principles]

### Rationale
[Why + evidence]

### Action
[What to do / alternative]
```

---

## ACCEPT Example

### Feedback
"This 100-line function violates SRP. Split into separate functions."

### Code Analysis
```typescript
function processOrder(order: Order): Result {
    // Validation (20 lines)
    if (!order.items.length) throw new Error();
    // ... validation logic

    // Calculation (30 lines)
    const total = order.items.reduce(...);
    // ... calculation logic

    // Persistence (25 lines)
    db.order.create({ ...order, total });
    // ... save logic

    // Notification (25 lines)
    sendEmail(order.userEmail, message);
    // ... notification logic
}
```

**Issues**:
- 100+ lines
- 4 responsibilities: validation, calculation, persistence, notification

### Evaluation
Aligns with **SRP (Single Responsibility Principle)** and **Clean Code**.

### Rationale
1. **SRP violation**: 4 distinct responsibilities
2. **Clean Code**: Functions should be ~20 lines
3. **Real benefit**: Better testability, reusability, readability

### Action
Accept and refactor:

```typescript
function processOrder(order: Order): Result {
    validateOrder(order);
    const total = calculateTotal(order);
    const savedOrder = saveOrder(order, total);
    notifyUser(savedOrder);
    return savedOrder;
}

function validateOrder(order: Order): void {
    if (!order.items.length) throw new Error("Empty order");
}

function calculateTotal(order: Order): number {
    return order.items.reduce((sum, item) => sum + item.price, 0);
}

function saveOrder(order: Order, total: number): Order {
    return db.order.create({ ...order, total });
}

function notifyUser(order: Order): void {
    sendEmail(order.userEmail, buildOrderMessage(order));
}
```

**Benefits**:
- ✅ Single responsibility per function
- ✅ Individually testable
- ✅ Function names document intent

---

## REJECT Example

### Feedback
"Variable name too short. Rename `user` to `authenticatedUserObject`."

### Code Analysis
```typescript
function sendEmail(user: User, message: string): void {
    mailer.send({
        to: user.email,
        subject: "Notification",
        body: message
    });
}
```

**Current state**:
- Scope: 5 lines
- Type: `User` clearly declared
- Context: Email sending, obvious

### Evaluation
**Subjective preference** lacking objective basis.

### Rationale
1. **Clean Code**: "Short names appropriate in short scopes" (Robert C. Martin)
2. **Type clarity**: `User` type conveys meaning
3. **Unnecessary verbosity**: `authenticatedUserObject` is excessive

**Clean Code principle**:
> "Variable name length should be proportional to scope size."

### Action
Reject. Keep current code.

**Reasons**:
- ✅ Follows Clean Code principles
- ✅ TypeScript type provides clarity
- ✅ Short scope = short name appropriate

---

## NEGOTIATE Example

### Feedback
"Add try-catch to all functions for error handling."

### Code Analysis
```typescript
// Service Layer
async function getUser(id: string): Promise<User> {
    return await db.user.findUnique({ where: { id } });
}

async function createOrder(data: OrderDto): Promise<Order> {
    return await db.order.create({ data });
}
```

### Evaluation
**"Error handling needed"** is valid, but **"try-catch everywhere"** isn't optimal.

**Valid**:
- ✅ Error handling is essential
- ✅ Current code lacks error handling

**Issues**:
- ⚠️ Try-catch in every function = duplication
- ⚠️ Violates Separation of Concerns
- ⚠️ Service layer shouldn't handle presentation

### Rationale
1. **Valid concern**: Error handling necessary
2. **Issue**: Different layers have different roles
   - Controller: Convert errors to HTTP responses
   - Service: Business logic
   - Repository: Data access
3. **Better approach**: Global error handler + layer-based propagation

### Action
Propose alternative:

```typescript
// 1. Global Error Handler (Controller Layer)
class ErrorHandler {
    handle(error: Error, res: Response) {
        if (error instanceof ValidationError) {
            return res.status(400).json({ message: error.message });
        }
        if (error instanceof NotFoundError) {
            return res.status(404).json({ message: error.message });
        }
        return res.status(500).json({ message: "Internal error" });
    }
}

// 2. Controller handles errors
class UserController {
    async getUser(req: Request, res: Response) {
        try {
            const user = await userService.getUser(req.params.id);
            res.json(user);
        } catch (error) {
            errorHandler.handle(error, res);
        }
    }
}

// 3. Service focuses on business logic (propagates errors)
class UserService {
    async getUser(id: string): Promise<User> {
        const user = await userRepository.findById(id);
        if (!user) throw new NotFoundError("User not found");
        return user;
    }
}

// 4. Repository handles data access
class UserRepository {
    async findById(id: string): Promise<User | null> {
        return await db.user.findUnique({ where: { id } });
    }
}
```

**Advantages**:
- ✅ Separation of Concerns maintained
- ✅ No error handling duplication
- ✅ Clear layer responsibilities
- ✅ Easy maintenance (change once)

**vs Original Feedback**:
- Global handler instead of try-catch everywhere
- Service focuses on business logic
- Controller handles error presentation

---

## Principle Conflict: DRY vs Domain Separation

### Feedback
"Remove duplicate email validation per DRY principle."

### Code Analysis
```typescript
// User domain
class UserValidator {
    validateEmail(email: string): boolean {
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }
}

// Newsletter domain
class NewsletterValidator {
    validateEmail(email: string): boolean {
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }
}
```

### Evaluation
**DRY** vs **Domain Separation** conflict.

**Decision**: Prioritize **Domain Separation**.

### Rationale
1. **Domain independence**: User and Newsletter are different bounded contexts
2. **Evolution potential**: May need different rules later
   - User: Company domain only
   - Newsletter: All domains allowed
3. **DDD principle**: "Separate when domains differ, even if code is same"

### Action
Keep current but extract common technical logic:

```typescript
// Common utility (pure technical logic)
class EmailFormat {
    static isValid(email: string): boolean {
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }
}

// User domain (with domain rules)
class UserValidator {
    validateEmail(email: string): boolean {
        return EmailFormat.isValid(email) && this.isAllowedDomain(email);
    }

    private isAllowedDomain(email: string): boolean {
        return email.endsWith("@company.com");
    }
}

// Newsletter domain (different rules)
class NewsletterValidator {
    validateEmail(email: string): boolean {
        return EmailFormat.isValid(email); // All domains OK
    }
}
```

**Trade-offs**:
- ✅ Domain independence maintained
- ✅ Independent evolution possible
- ⚠️ Some code duplication allowed
- 📊 Long-term maintainability > DRY

---

## Best Practices

### Be Objective
❌ "This is nonsense"
✅ "Per Clean Code principles..."

### Be Specific
❌ "Seems better"
✅ "Aligns with SRP because..."

### Be Constructive
❌ "Wrong, rejected"
✅ "Alternative approach: [proposal]"

---

**Last Updated**: 2026-01-25
**Version**: 3.0.0
