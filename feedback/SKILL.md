---
name: critical-acceptance
description: Critically accept user feedback — don't blindly follow or reject, evaluate against objective principles before acting
---

# Critical Acceptance Skill (비판적수용)

Critically accept user feedback based on **objective principles** rather than blindly following or rejecting it.

## Core Principles

```
Priority 1: Objective principles (Clean Code, SOLID, Architecture)
Priority 2: Actual code behavior/intent
Priority 3: Feedback content
Priority 4: User/project preferences (reference only)
```

**Philosophy**:
- Feedback = subjective opinion (don't accept unconditionally)
- Code = primary evidence (working code is truth)
- All opinions are subject to verification (reviewer, user, AI included)

## Execution Steps

### 1. Understand Feedback

- **Who** provided it? (reviewer, teammate, user)
- **What** do they suggest/criticize?
- **Why** do they think so? (evidence provided?)

### 2. Analyze Code

Read actual code (no guessing), search related code.

**Analysis points**:
- Does the code actually work?
- What is the intent?
- Pros/cons of current structure?

### 3. Evaluate with Objective Principles

See `references/objective-principles.md`:
- **Clean Code**: readability, simplicity, consistency
- **SOLID**: SRP, OCP, LSP, ISP, DIP
- **Architecture**: layering, separation of concerns, DRY

### 4. Validate Feedback

Use checklist from `references/evaluation-criteria.md`:

| Criterion | Question |
|-----------|----------|
| **Objectivity** | Based on objective principles? |
| **Specificity** | Concrete problem and solution? |
| **Validity** | Technically correct? |
| **Practicality** | Real benefit exists? |
| **Consistency** | Aligned with project? |

### 5. Verify User Intent

**Important**: User opinions are also subject to verification
- If violates objective principles → point out
- If differs from actual code behavior → correct
- If inconsistent with codebase → verify

### 6. Make Decision & Respond

See `references/examples.md` for detailed examples.

**ACCEPT** ✅
```
Condition: Aligns with objective principles + real improvement
Action: Apply feedback
```

**REJECT** ❌
```
Condition: Violates principles OR subjective preference
Action: Keep current code + provide rationale
```

**NEGOTIATE** 🤝
```
Condition: Partially valid OR alternative exists
Action: Propose alternative and discuss
```

## References

- **[evaluation-criteria.md](references/evaluation-criteria.md)**: 5 evaluation dimensions in detail
- **[objective-principles.md](references/objective-principles.md)**: Clean Code, SOLID principles in detail
- **[examples.md](references/examples.md)**: Detailed Accept/Reject/Negotiate examples
