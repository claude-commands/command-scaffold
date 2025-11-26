# command-scaffold

A Claude Code slash command for generating project scaffolding following existing patterns.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-scaffold.git <clone-path>/command-scaffold

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-scaffold/scaffold.md ~/.claude/commands/scaffold.md
```

## Usage

```text
/scaffold component Button     # React/Vue component
/scaffold api users            # API endpoint with CRUD
/scaffold model User           # Database model
/scaffold service payment      # Service class
/scaffold hook useAuth         # Custom hook
/scaffold test auth            # Test file
```

## What it does

1. Detects project framework and patterns
2. Finds existing similar files
3. Generates files following conventions
4. Creates tests automatically
5. Updates exports and routes

## Scaffold Types

| Type | Creates |
|------|---------|
| component | Component with styles and tests |
| api | Route handler with CRUD operations |
| model | Database model/entity |
| service | Service class |
| hook | React custom hook |
| test | Test file |
| middleware | Express middleware |
| schema | Validation schema |

## Example Output

```markdown
# Scaffolding Complete

## Created Files
| File | Type |
|------|------|
| src/components/Button/Button.tsx | Component |
| src/components/Button/Button.test.tsx | Test |
| src/components/Button/index.ts | Export |

## Updated Files
- src/components/index.ts (added export)
```

## Framework Support

| Framework | Scaffolds |
|-----------|-----------|
| React | Components, hooks, context |
| Express | Routes, middleware, services |
| Prisma/TypeORM | Models, migrations |
| Jest/Vitest | Test files |

## Requirements

- Git repository with source code
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-scaffold && git pull
```
