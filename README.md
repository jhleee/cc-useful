# skills-repo

A collection of practical skills for Claude Code.

## Skills

### feedback

Critically evaluate code review feedback with objective principles rather than subjective opinions.

- **Accept**: feedback that aligns with Clean Code, SOLID, and architecture principles
- **Reject**: feedback based on subjective preference with clear rationale
- **Negotiate**: propose alternatives when feedback is partially valid

Uses a structured 6-step evaluation process with reference materials for objective principles, evaluation criteria, and worked examples.

## Installation

Install from GitHub:

```bash
claude plugin add -- jhleee/skills-repo
```

Or install from a local directory:

```bash
claude plugin add /path/to/skills-repo
```

## Usage

Invoke the feedback skill when receiving code review comments:

```
/feedback
```

Or it will trigger automatically when Claude detects code review feedback evaluation context.

## Author

jhleee
