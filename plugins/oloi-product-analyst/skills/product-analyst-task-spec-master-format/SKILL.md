---
name: product-analyst-task-spec-master-format
description: Generates definitive, high-detail engineering task specs using a fixed, non-negotiable format for backend/frontend/product work. Use this whenever the user asks to create a task/ticket/spec and expects consistent structure and acceptance rigor.
when_to_use:
  - creating Jira tickets
  - writing implementation tasks for developers
  - converting product requirements into technical specs
  - defining acceptance criteria, DoD, and test plans
---

# Task Spec Master Format (Definitive)

This skill enforces a fixed, high-quality task specification format.
Do not improvise structure.
Do not skip sections unless explicitly marked "N/A".
Do not invent business rules; use only provided context and clearly state assumptions.

## Core Principles

1. **No invention**: If requirement data is missing, write explicit assumptions/open questions.
2. **Deterministic format**: Always use the exact section order for the selected template.
3. **Execution-ready**: Task must be implementable by a developer without follow-up.
4. **Verifiable outcomes**: Acceptance criteria and tests must be objective.
5. **Architecture-aware**: Include authorization/isolation/caching/events/observability where applicable.
6. **Scope discipline**: Explicit In/Out boundaries.

---

## Output Language

- Match the user language.
- Keep terminology consistent with user domain.
- Use concise but complete technical writing.

---

## Template Selector

Choose template by task nature:

- **Backend/API task** -> Use `BACKEND TEMPLATE`.
- **Frontend/UI task** -> Use `FRONTEND TEMPLATE`.
- **Mixed full-stack task** -> Use `FULL-STACK TEMPLATE`.
- **If unclear** -> choose full-stack and mark non-applicable subsections as `N/A`.

---

## BACKEND TEMPLATE (mandatory order)

Start with:
`Skill aplicado: \`laravel-backend-task-spec-writer\` para definir la tarea con estándar técnico y consistencia arquitectónica.`

Then output exactly:

## 1) Title
One-line implementation title with verb + object.

## 2) Context
- Existing endpoints/modules/behavior.
- Current gaps/problems.
- Constraints provided by user.

## 3) Goal
What success looks like in one paragraph.
Include critical business/technical rule summary.

## 4) Scope
In:
- bullet list of included deliverables

Out:
- bullet list of explicitly excluded items

## 5) API Contract
Include only if endpoint-affecting.
- Route(s), method(s), path params, query params, body schema
- Success response shape
- Error responses by status code

If not API-related: `N/A`.

## 6) Authorization & Isolation
- Policy rule(s)
- Ownership checks
- Tenant/branch/company isolation behavior
- Security leakage rules (`404` vs `403` where relevant)

If not applicable: `N/A`.

## 7) Architecture Plan
Breakdown by:
- Controller/interface layer
- Service/application layer
- Repository/port
- Persistence/migrations
- File targets (absolute or repo-resolved paths)

## 8) Caching Plan
- What is cached/not cached
- Invalidation strategy
- If none: `No aplica en esta fase.`

## 9) Events Plan
- Domain/integration events emitted or explicitly none
- Timing (before/after commit)
- If none: `No aplica en esta fase.`

## 10) Observability
- Structured log events names
- Required context fields
- Error logging guidance

## 11) Tests (Mandatory)
Must include:
- Feature tests matrix
- Unit tests matrix
- Policy/authorization tests
- Isolation tests if multi-context exists
- Repository assertions when relevant

## 12) OpenAPI
- Required annotations/paths/responses/security
- If not applicable: `N/A`.

## 13) Acceptance Criteria
Numbered, objective, testable statements only.

## 14) Definition of Done
Checklist format:
- [ ] item
- [ ] item

## 15) Skill Selection Plan
Ordered list of skills to apply.
Include “No aplicar” subsection for irrelevant skills.

---

## FRONTEND TEMPLATE (mandatory order)

Start with:
`Skill aplicado: \`product-analyst-task-breakdown-writer\` para descomponer la tarea funcional y técnica con criterios verificables.`

Then output exactly:

## Task Title
Clear UI implementation title.

## Objetivo
What the page/module must achieve.

## Context
Current state, known issues, constraints.

## Endpoints a consumir
List method + route + purpose for each endpoint.
If none, write `N/A`.

## Alcance funcional
Numbered list of user-visible behaviors.

## Entregables frontend
Concrete components/modules to deliver.

## Modelo de estado recomendado
Typed state contract (TS-style) when useful.

## Contrato UI <-> API
Payloads, mapping rules, parsing expectations.

## Reglas de validación en frontend
Numbered deterministic validations.

## UX/Estados requeridos
Loading/saving/success/error/dirty/discard behavior.

## Comportamiento específico por tipo/escenario
Conditional rendering/logic matrix.

## Criterios de aceptación
Numbered and objectively verifiable.

## Testing frontend mínimo
- Unit
- Integration/component
- Optional E2E

## Dependencias / bloqueos
External blockers and prerequisites.

## Definition of Done
Checklist format:
- [ ] item
- [ ] item

## Skill Selection Plan
Ordered skills and “No aplicar” list.

---

## FULL-STACK TEMPLATE (mandatory order)

Use backend structure plus frontend sections.
Order:

1. Title  
2. Context  
3. Goal  
4. Scope (In/Out)  
5. API Contract  
6. Authorization & Isolation  
7. Backend Architecture Plan  
8. Frontend Architecture Plan  
9. UI/State Model  
10. Validation Rules (Backend + Frontend)  
11. Caching Plan  
12. Events Plan  
13. Observability  
14. Tests (Backend + Frontend + E2E)  
15. OpenAPI  
16. Acceptance Criteria  
17. Definition of Done  
18. Skill Selection Plan  

---

## Quality Gates (must pass before finalizing)

Before delivering, verify all:

1. Every requirement in user context is mapped to at least one section.
2. No ambiguous terms like “etc.” without explicit items.
3. Acceptance criteria are testable, not aspirational.
4. DoD checklist aligns with architecture + tests + docs.
5. “Out of scope” prevents uncontrolled expansion.
6. If assumptions exist, they are explicitly labeled.

---

## Assumptions & Open Questions Protocol

If missing data blocks precision, include:

### Assumptions
- assumption 1
- assumption 2

### Open Questions
- question 1
- question 2

Do not halt unless user explicitly requests questions first.

---

## Anti-Patterns (forbidden)

- Changing section order.
- Omitting Tests or DoD.
- Inventing endpoints/permissions not implied by context.
- Mixing optional nice-to-haves into mandatory scope without labeling.
- Vague AC like “works fine”.

---

## Mini Examples (headers only)

### Backend
1) Title  
2) Context  
...  
15) Skill Selection Plan

### Frontend
Task Title  
Objetivo  
Context  
...  
Skill Selection Plan

---

## Final Instruction

When user asks for a task/ticket/spec:
1. Pick template.
2. Fill every section in order.
3. Keep strict format.
4. Mark non-applicable sections as `N/A`.
5. Ensure implementation-ready depth.
```