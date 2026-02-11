# Feedback Evaluation Criteria

Checklist for evaluating feedback across 5 dimensions.

## 1. Objectivity

**Question**: Based on objective principles or subjective preference?

| Objective ✅ | Subjective ❌ |
|--------------|---------------|
| "Violates SRP - class has 4 responsibilities" | "I prefer this style" |
| "O(n²) complexity causes performance issues" | "Feels slow" |
| "TypeScript strict mode error on line 42" | "Types look complex" |
| "Fails WCAG 2.1 AA accessibility standard" | "Don't like the color" |

**Decision**:
- ✅ Accept: References objective principles/standards
- ⚠️ Negotiate: Mix of objective + subjective
- ❌ Reject: Pure subjective preference

## 2. Specificity

**Question**: Provides concrete problem and solution?

| Specific ✅ | Vague ❌ |
|-------------|----------|
| "Split `getUserData` into `fetchUser`, `validateUser`, `transformUser`" | "Refactor this code" |
| "Add `minLength: 8` validation to password field" | "Improve security" |
| "Line 63: Replace `map` chain with `reduce` for better performance" | "Performance is bad" |
| "Remove console.log or disable ESLint 'no-console' rule" | "Code is messy" |

**Decision**:
- ✅ Accept: Specific location, method, rationale
- ⚠️ Negotiate: Problem clear but solution vague
- ❌ Reject: Abstract and unactionable

## 3. Validity

**Question**: Technically correct and contextually appropriate?

### Technical Accuracy

| Valid ✅ | Invalid ❌ |
|----------|------------|
| "`await` requires `async` function" | "async makes everything faster" |
| "React Hooks can't be called in conditions" | "Hooks at top improve performance" |
| "Use spread operator for immutable updates" | "Spread is always fastest" |

### Context Fit

| Contextual ✅ | Context-Ignored ❌ |
|---------------|---------------------|
| "Single-server bot doesn't need sharding" | "Always add sharding" |
| "Prototype can use simple implementation" | "Must use enterprise architecture" |
| "Test-only function export is appropriate" | "Remove unused export" |

**Decision**:
- ✅ Accept: Technically accurate + fits context
- ⚠️ Negotiate: Technically sound but context needs discussion
- ❌ Reject: Technical error OR context mismatch

## 4. Practicality

**Question**: Real benefit vs cost?

### Cost-Benefit Analysis

| Cost | Benefit | Decision | Example |
|------|---------|----------|---------|
| Low | High | ✅ Accept | Fix obvious bug |
| Low | Medium | ✅ Accept | Improve variable names |
| Medium | High | ✅ Accept | Architecture improvement |
| High | High | ⚠️ Negotiate | Full refactor (phased approach) |
| Medium | Low | ❌ Reject | Unnecessary abstraction |
| High | Low | ❌ Reject | Premature optimization |

### Real vs Hypothetical Problems

| Real Problem ✅ | Hypothetical Problem ❌ |
|-----------------|-------------------------|
| "Takes 30s for 1M records now" | "Might need to handle 1B someday" |
| "Production error happening" | "Theoretically could error" |
| "User-requested feature" | "Might need this feature later" |

**Decision**:
- ✅ Accept: Real problem + reasonable cost
- ⚠️ Negotiate: Benefit exists but cost negotiable
- ❌ Reject: YAGNI (You Aren't Gonna Need It)

## 5. Consistency

**Question**: Aligns with project conventions and principles?

### Codebase Consistency

Check project patterns:
```bash
Grep "similar pattern"              # Find similar code
Read .eslintrc / .prettierrc        # Check style config
Read src/modules/[other-module]     # See other modules
```

| Consistent ✅ | Inconsistent ❌ |
|---------------|-----------------|
| "Other modules use Service pattern, follow it" | "Do this module differently" |
| "ESLint requires no semicolons" | "Add semicolons here only" |
| "Project uses functional style throughout" | "Use classes here only" |

### Principle Consistency

Feedback shouldn't contradict other principles:

| Consistent ✅ | Contradictory ❌ |
|---------------|------------------|
| "Remove duplication, but keep domain boundaries" | "Remove all duplication" (ignores context) |
| "Add type safety + runtime validation" | "Types mean no validation needed" |

**Decision**:
- ✅ Accept: Aligns with project + principles
- ⚠️ Negotiate: Partial alignment, adjustable
- ❌ Reject: Completely inconsistent

## Evaluation Matrix

### Accept Criteria (3+ met)
- [x] Objective evidence clear
- [x] Specific problem and solution
- [x] Technically valid
- [x] Real benefit exists
- [x] Project-consistent

### Reject Criteria (1+ met)
- [ ] Subjective preference only
- [ ] Vague and unactionable
- [ ] Technically incorrect
- [ ] No real benefit
- [ ] Project-inconsistent

### Negotiate Criteria
- [ ] Partially valid
- [ ] Alternative exists
- [ ] Conditional acceptance

## Special Cases

### Case 1: Reviewer vs User Conflict
**Principle**: Follow objective evidence (ignore who said it)

```
Reviewer: "This aligns with Clean Code"
User: "No, my way is correct"

→ Check Clean Code principles, decide objectively
```

### Case 2: Principle Conflict
**Principle**: Context determines priority

```
DRY vs Domain Separation
→ Domain separation wins if domains differ

Performance vs Readability
→ Performance wins in bottlenecks
→ Readability wins elsewhere
```

### Case 3: Prototype vs Production
**Principle**: Match quality to current phase

```
Prototype:
  ✅ Fast validation, simple implementation
  ❌ Over-engineering, premature optimization

Production:
  ✅ Robust architecture, comprehensive tests
  ❌ Quick hacks, skipped validation
```

---

**Last Updated**: 2026-01-25
**Version**: 3.0.0
