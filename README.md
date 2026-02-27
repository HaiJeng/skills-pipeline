# OpenSpec Skills Pipeline

Claude Code skills for Spec-Driven Development workflow.

## Skills

| Skill | Description | Pipeline Phase |
|-------|-------------|----------------|
| `prd-standardizer` | Converts unstructured PRD/notes to structured standard PRD | Phase 1: Semantic Organization |
| `spec-generator` | Converts structured PRD to OpenSpec change spec | Phase 2: Spec Compilation |
| `task-decomposer` | Decomposes spec into atomic task graph | Phase 3: Execution Planning |

## Usage with cc-switch

1. Open cc-switch → Click "Skills"
2. Add custom repository: `https://github.com/<your-username>/skills-pipeline`
3. Install desired skills with one click

## Pipeline Flow

```
Raw PRD
   ↓
prd-standardizer
   ↓
Structured PRD
   ↓
spec-generator
   ↓
OpenSpec Change
   ↓
task-decomposer
   ↓
Atomic Task Graph
```

## Output Formats

### prd-standardizer
- Background, Goals, Non-Goals
- User Personas, User Stories
- Functional Requirements, Non-Functional Requirements
- Constraints, Assumptions, Edge Cases, Open Questions

### spec-generator
- ADDED/MODIFIED/REMOVED Requirements
- Acceptance Criteria (Given/When/Then)
- Domain Concepts, Invariants

### task-decomposer
- Task List with dependencies
- Parallelizable Groups