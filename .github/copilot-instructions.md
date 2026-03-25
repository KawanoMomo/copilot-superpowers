## Superpowers for GitHub Copilot

This repository provides systematic development workflow skills for GitHub Copilot Agent Mode.

### Skill Priority

When multiple skills could apply:
1. **Process skills first** (brainstorming, debugging) — determine HOW to approach
2. **Implementation skills second** — guide execution

### Key Workflows

- **New feature:** `/brainstorm` -> `/write-plan` -> `/execute-plan`
- **Bug fix:** `/debug` -> TDD fix
- **Before completion:** Always verify (verification-before-completion skill)
- **Before merge:** Always review (requesting-code-review skill)

### Agent Workflow (handoffs)

Planner -> Implementer -> Spec Reviewer -> Code Reviewer -> Back to Planner or Finish

### Iron Laws

1. NO production code without a failing test first (TDD)
2. NO fixes without root cause investigation first (Debugging)
3. NO completion claims without fresh verification evidence (Verification)
