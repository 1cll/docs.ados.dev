# Writing copilot-instructions.md

`copilot-instructions.md` is a file that provides repository-specific rules and context to AI agents. ADOS reads this file to significantly improve AI implementation quality.

## File Location

```
.github/copilot-instructions.md
```

Place it inside the `.github/` directory at the root of your repository.

## Basic Structure

```markdown
# Project Overview
This repository is the backend API for ○○.

# Tech Stack
- Language: TypeScript
- Framework: Express.js
- Database: PostgreSQL + Prisma
- Testing: Jest

# Coding Conventions
- Use camelCase for variable names
- Write functions as arrow functions
- Always use try-catch for error handling

# File Structure
- `src/controllers/` — API controllers
- `src/services/` — Business logic
- `src/models/` — Prisma models
- `src/middleware/` — Express middleware

# Testing Rules
- Write unit tests for all service functions
- Place test files in the `__tests__/` directory
- Write descriptive test names

# Prohibited Patterns
- No `any` types
- No `console.log` (use the logger)
- No raw SQL queries (use Prisma)
```

## Tips for Writing Effective Instructions

### 1. Be Specific

❌ Bad example:
```markdown
Write clean code
```

✅ Good example:
```markdown
- Keep functions under 30 lines
- Max 3 levels of nesting
- Use early return pattern
```

### 2. Describe the Architecture

```markdown
# Architecture
- Uses Clean Architecture
- Layers: Controller → UseCase → Repository
- Dependency direction: outer → inner
- DI container: tsyringe
```

### 3. Show Existing Patterns

```markdown
# How to Add a New API Endpoint
1. Create a new controller in `src/controllers/`
2. Create the corresponding service in `src/services/`
3. Add routing in `src/routes.ts`
4. Create tests in `src/__tests__/`

Reference: `src/controllers/users.controller.ts`
```

### 4. List Prohibited Patterns

```markdown
# Things to Avoid
- Global variables
- Synchronous file operations (e.g., fs.readFileSync)
- Direct environment variable access (use the config module)
- Magic numbers
```

### 5. Language/Framework-Specific Rules

```markdown
# React Rules
- Do not use class components (Functional Components only)
- Use useState / useReducer for state management
- Use Zustand for global state
- Use Tailwind CSS utility classes

# API Response Format
All APIs must return responses in this format:
{
  "success": true,
  "data": { ... },
  "error": null
}
```

## Other Files ADOS References

| File | Purpose |
|------|---------|
| `.github/copilot-instructions.md` | Main instruction file |
| `.ados.yaml` | ADOS-specific configuration |
| `README.md` | Referenced as project overview |
| `CONTRIBUTING.md` | Contributing guidelines |

> [!TIP]
> The quality of `copilot-instructions.md` directly impacts AI implementation quality. Aim for the same level of detail you would provide in onboarding documentation for new team members.
