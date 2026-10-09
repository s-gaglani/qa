# AIDLC Frontend Developer Agent — Complete Reference

> **Agent ID:** `aidlc-frontend-developer`  
> **Installed In:** Apex (Kiro IDE Extension)  
> **Version:** Defined in `~/.kiro/agents/aidlc-frontend-developer.json`  
> **Author:** architecture-team / aidlc-framework

---

## 1. Overview

The **aidlc-frontend-developer** is a specialist sub-agent within the AI-Driven Development Lifecycle (AI-DLC) framework. It is responsible for generating client-side code — HTML, CSS, JavaScript, React components, responsive layouts, animations, and accessible UI — during the **Construction Phase → Code Generation** stage.

It is **never invoked directly by the user**. The **aidlc-orchestrator** agent delegates work to it when the workflow reaches the Code Generation stage and the unit of work is frontend-scoped.

---

## 2. Agent Configuration

```json
{
  "name": "aidlc-frontend-developer",
  "description": "AI-DLC frontend developer for HTML, CSS, JS, React, accessibility.",
  "prompt": "file://./aidlc-frontend-developer.md",
  "tools": ["read", "write", "shell"],
  "allowedTools": ["read"],
  "resources": [
    "file://.kiro/steering/**/*.md",
    "skill://.kiro/skills/**/SKILL.md",
    "file://~/.kiro/steering/**/*.md",
    "skill://~/.kiro/skills/**/SKILL.md"
  ]
}
```

| Property | Value | Purpose |
|----------|-------|---------|
| `tools` | `read`, `write`, `shell` | Can read files, write code, run shell commands |
| `allowedTools` | `read` | Auto-approved without user confirmation |
| `resources` | Workspace + user steering & skills | Access to all enterprise rules and project context |

---

## 3. Role & Responsibilities

```
┌─────────────────────────────────────────────────┐
│         AIDLC Frontend Developer                │
├─────────────────────────────────────────────────┤
│ • Generate HTML, CSS, JavaScript from design    │
│   artifacts                                     │
│ • Build responsive, accessible, performant UI   │
│   components                                    │
│ • Implement React/framework-based UIs           │
│ • Add animations, transitions, interactive      │
│   features                                      │
│ • Ensure semantic HTML & ARIA compliance        │
│ • Implement keyboard navigation & focus mgmt    │
│ • Validate colour contrast (WCAG 2.1 AA)       │
│ • Add data-testid attributes for automation     │
│ • Apply enterprise standards (MUST) & best      │
│   practices (SHOULD) to frontend code           │
│ • Follow approved code generation plan exactly  │
└─────────────────────────────────────────────────┘
```

---

## 4. Position in the AI-DLC Lifecycle

```mermaid
flowchart LR
    subgraph INCEPTION["Phase 1: INCEPTION"]
        A[Requirements Analysis] --> B[User Stories]
        B --> C[Application Design]
        C --> D[Units Generation]
    end

    subgraph CONSTRUCTION["Phase 2: CONSTRUCTION"]
        E[Functional Design] --> F[NFR Requirements]
        F --> G[NFR Design]
        G --> H[Infrastructure Design]
        H --> I[Code Generation]
        I --> J[Build & Test]
    end

    subgraph OPERATIONS["Phase 3: OPERATIONS"]
        K[Deployment]
        L[ORP]
    end

    D --> E
    J --> K

    style I fill:#61dafb,stroke:#333,color:#000
```

> The **Frontend Developer** agent is activated at the **Code Generation** stage (highlighted above). It executes only after Functional Design, NFR Design, and Infrastructure Design are complete for the unit.

---

## 5. Invocation & Delegation Flow

```mermaid
sequenceDiagram
    participant User
    participant Orchestrator as aidlc-orchestrator
    participant Frontend as aidlc-frontend-developer
    participant Skills as Enterprise Skills
    participant A11y as Accessibility Audit

    User->>Orchestrator: Continue to Code Generation
    Orchestrator->>Orchestrator: Check delegation matrix
    Orchestrator->>Orchestrator: Log: DELEGATED → frontend-developer
    Orchestrator->>Frontend: Spawn with unit context + design artifacts

    Frontend->>Skills: Load aidlc-ent-stn (mandatory standards)
    Frontend->>Skills: Load aidlc-ent-bp (best practices)
    Frontend->>Skills: Load aidlc-rules (stage rules)
    Frontend->>A11y: Load aidlc-accessibility-audit
    Frontend->>Frontend: PART 1 — Create code generation plan
    Frontend->>User: Present plan for approval

    User->>Frontend: Approve plan
    Frontend->>Frontend: PART 2 — Execute plan step-by-step
    Frontend->>Frontend: Generate UI components, styles, tests
    Frontend->>Frontend: Validate accessibility compliance
    Frontend->>User: Present completion + review request

    User->>Frontend: Approve generated code
    Frontend->>Orchestrator: Return results
    Orchestrator->>Orchestrator: Persist artifacts, update state
    Orchestrator->>User: Present result + next stage options
```

---

## 6. Two-Part Execution Workflow

```mermaid
flowchart TD
    subgraph PART1["PART 1: PLANNING"]
        P1[Step 1: Analyze Unit Context & Design Artifacts] --> P2[Step 2: Create Detailed Frontend Plan]
        P2 --> P3[Step 3: Include Component Hierarchy]
        P3 --> P4[Step 4: Save Plan Document]
        P4 --> P5[Step 5: Summarize Plan to User]
        P5 --> P6[Step 6: Log Approval Prompt]
        P6 --> P7[Step 7: Wait for User Approval]
        P7 --> P8[Step 8: Record Approval]
        P8 --> P9[Step 9: Update Progress]
        P9 --> P9J[Step 9.1: Jira Sub-tasks — if enabled]
    end

    subgraph PART2["PART 2: GENERATION"]
        G10[Step 10: Load Plan, Find Next Step] --> G11[Step 11: Execute Current Step]
        G11 --> G12[Step 12: Add data-testid Attributes]
        G12 --> G13[Step 13: Validate Accessibility]
        G13 --> G14[Step 14: Update Progress]
        G14 --> G15{More Steps?}
        G15 -->|Yes| G10
        G15 -->|No| G16[Step 16: Present Completion]
        G16 --> G17[Step 17: Wait for Approval]
        G17 --> G18[Step 18: Record & Finalize]
    end

    P9J --> G10

    style PART1 fill:#e6f3ff,stroke:#0066cc
    style PART2 fill:#e6ffe6,stroke:#009900
```

---

## 7. Skills & Steering Files Used

### 7.1 Skills Loaded at Runtime

```mermaid
graph TD
    FD[aidlc-frontend-developer] --> ENT_STN[aidlc-ent-stn
Enterprise Standards]
    FD --> ENT_BP[aidlc-ent-bp
Enterprise Best Practices]
    FD --> RULES[aidlc-rules
Stage Rules]
    FD --> A11Y[aidlc-accessibility-audit
Accessibility Audit]

    ENT_STN --> STN1[ent-stn-iam.md
Identity & Access Mgmt]
    ENT_STN --> STN2[ent-stn-secrets.md
Secrets Management]
    ENT_STN --> STN3[ent-stn-encryption.md
Encryption Standards]
    ENT_STN --> STN4[ent-stn-logging.md
Logging & Monitoring]
    ENT_STN --> STN5[ent-stn-environments.md
Environment Separation]
    ENT_STN --> STN6[ent-stn-devops.md
CI/CD Standards]
    ENT_STN --> STN7[arch-stn-822-iam.md
STN-822 Okta/OIDC]
    ENT_STN --> STN8[arch-stn-828-data.md
STN-828 Data Architecture]
    ENT_STN --> STN9[arch-stn-cloud-aws.md
AWS Cloud Standards]
    ENT_STN --> STN10[arch-stn-aws-services.md
AWS IaC Standards]

    ENT_BP --> BP1[api-design.md]
    ENT_BP --> BP2[containers.md]
    ENT_BP --> BP3[observability.md]
    ENT_BP --> BP4[resiliency.md]
    ENT_BP --> BP5[healthchecks.md]
    ENT_BP --> BP6[integration-testing.md]

    RULES --> CG[construction/code-generation.md]

    A11Y --> AC1[Semantic HTML]
    A11Y --> AC2[Keyboard Navigation]
    A11Y --> AC3[Colour & Contrast]

    style FD fill:#61dafb,stroke:#333,color:#000
    style ENT_STN fill:#fde8e8,stroke:#633
    style ENT_BP fill:#dceefb,stroke:#336
    style RULES fill:#e8f5e9,stroke:#363
    style A11Y fill:#fff3cd,stroke:#856404
```

### 7.2 Skill Details

| Skill | Type | Purpose | Relevance to Frontend |
|-------|------|---------|----------------------|
| `aidlc-ent-stn` | Standards (MUST) | Mandatory requirements for IAM/Okta, secrets, encryption, logging, CI/CD | Token handling, OIDC flows, secure storage, CSP headers |
| `aidlc-ent-bp` | Best Practices (SHOULD) | Recommended patterns for API design, observability, resiliency | Error handling, health checks, retry logic on API calls |
| `aidlc-rules` | Stage Rules | Code generation workflow steps, checkboxes, approval gates | Governs plan structure, progress tracking, approval flow |
| `aidlc-accessibility-audit` | Accessibility (WCAG) | Semantic HTML, ARIA, keyboard navigation, colour contrast | Core to every UI component generated |

### 7.3 Accessibility Audit Skill — Detail

The `aidlc-accessibility-audit` skill is uniquely associated with the frontend developer agent (metadata: `agents: frontend-developer, qa-engineer`). It enforces:

| Area | Requirements |
|------|-------------|
| **Semantic HTML** | Proper heading hierarchy (h1→h2→h3), landmark elements (nav, main, aside, footer), lists for navigation |
| **Keyboard Navigation** | All interactive elements focusable, tab order follows visual layout, visible focus indicators, Escape closes modals |
| **Colour & Contrast** | 4.5:1 minimum for normal text, 3:1 minimum for large text (WCAG 2.1 AA) |
| **ARIA** | Proper roles, labels, descriptions; live regions for dynamic content |
| **Screen Readers** | Content order matches visual order, alt text on images, form labels associated |

### 7.4 Steering Files Accessible

The agent has access to all steering files at both levels:

| Scope | Path Pattern | Content |
|-------|-------------|---------|
| Workspace | `.kiro/steering/**/*.md` | Project-specific context (tech stack, structure, product domain) |
| User/Global | `~/.kiro/steering/**/*.md` | Cross-project enterprise rules |

**Current workspace steering files available:**

| File | Content |
|------|---------|
| `tech.md` | Tech stack, commands, reporting pattern, environment config |
| `structure.md` | Project layout, naming conventions, isolation rules |
| `PRODUCT.md` | ETC/GTN/JETS domain context, trade flow, markets |
| `matching-context.md` | Market matching specific context |
| `uft-native-methods.md` | UFT native method references |

---

## 8. Code Generation Plan Structure

When the frontend developer creates a plan, it produces a document at:
```
aidlc-docs/construction/plans/{unit-name}-code-generation-plan.md
```

The plan contains these sequential steps (each with `[ ]` checkboxes):

| Step | Activity | Output |
|------|----------|--------|
| 1 | Project Structure Setup (greenfield only) | Directory skeleton, package.json, build config |
| 2 | Component Architecture | Component tree, props/state design |
| 3 | Base Layout & Routing | App shell, router config, page containers |
| 4 | Shared UI Components | Buttons, inputs, modals, cards (design system) |
| 5 | Feature Components | Business-logic UI, forms, data displays |
| 6 | Styling & Responsive Design | CSS/SCSS modules, breakpoints, theme |
| 7 | Animations & Transitions | Micro-interactions, loading states |
| 8 | State Management | Context/Redux/Zustand setup, API hooks |
| 9 | API Integration Layer | Service files, fetch hooks, error handling |
| 10 | Accessibility Hardening | ARIA, keyboard, focus management, contrast |
| 11 | Testing Automation Attributes | `data-testid` on all interactive elements |
| 12 | Unit/Component Tests | Test files for components |
| 13 | Documentation | Component docs, Storybook stories |
| 14 | Build & Deployment Artifacts | Dockerfile, CI config, env handling |

---

## 9. Critical Rules & Constraints

```mermaid
graph LR
    subgraph HARD_RULES["Hard Rules — Never Violate"]
        R1["Application code → workspace root ONLY"]
        R2["Documentation → aidlc-docs/ ONLY"]
        R3["Follow plan EXACTLY — no deviation"]
        R4["Brownfield: modify in-place, never duplicate"]
        R5["Update checkboxes after each step"]
        R6["Get explicit user approval before generating"]
        R7["Respect unit dependencies"]
        R8["Never write secrets/tokens in source"]
    end

    subgraph ACCESSIBILITY["Accessibility Rules — Always Apply"]
        A1["Semantic HTML over divs"]
        A2["ARIA labels on all interactive elements"]
        A3["Keyboard-navigable — no mouse-only UI"]
        A4["4.5:1 contrast ratio minimum"]
        A5["Visible focus indicators"]
        A6["Form inputs have associated labels"]
    end

    subgraph AUTOMATION["Automation-Friendly Rules"]
        T1["Add data-testid to ALL interactive elements"]
        T2["Naming: component-element-role"]
        T3["No dynamic/auto-generated IDs"]
        T4["Keep testid values stable across renders"]
    end

    style HARD_RULES fill:#fff3cd,stroke:#856404
    style ACCESSIBILITY fill:#d4edda,stroke:#155724
    style AUTOMATION fill:#cce5ff,stroke:#004085
```

### `data-testid` Naming Convention

```
data-testid="<component>-<element>-<role>"
```

**Examples:**
- `data-testid="login-form-submit-button"`
- `data-testid="trade-book-filter-dropdown"`
- `data-testid="order-input-quantity-field"`
- `data-testid="nav-sidebar-toggle"`

### Code Location Rules by Project Type

| Project Type | Code Location | Tests Location |
|--------------|--------------|----------------|
| Brownfield | Existing structure (`src/components/`, `app/`, etc.) | Existing test dirs |
| Greenfield Single Unit | `src/`, `tests/`, `public/` at workspace root | `tests/` or `__tests__/` |
| Greenfield Multi-Unit (Microfrontends) | `{unit-name}/src/` | `{unit-name}/tests/` |
| Greenfield Multi-Unit (Monolith) | `src/{unit-name}/` | `tests/{unit-name}/` |

---

## 10. Enterprise Compliance Check Flow (Frontend-Specific)

```mermaid
flowchart TD
    START[Start Frontend Code Generation] --> READ_INDEX[Read ent-ref-steering-index.md]
    READ_INDEX --> IDENTIFY[Identify applicable references
via trigger patterns]
    IDENTIFY --> LOAD_STN[Load relevant aidlc-ent-stn references]
    IDENTIFY --> LOAD_BP[Load relevant aidlc-ent-bp references]
    IDENTIFY --> LOAD_A11Y[Load aidlc-accessibility-audit]
    LOAD_STN --> APPLY[Apply patterns during generation]
    LOAD_BP --> APPLY
    LOAD_A11Y --> APPLY
    APPLY --> CHECK_IAM{Uses auth/tokens?}
    CHECK_IAM -->|Yes| APPLY_IAM[Apply STN-822 OIDC
token handling]
    CHECK_IAM -->|No| CHECK_SEC
    APPLY_IAM --> CHECK_SEC{Stores data client-side?}
    CHECK_SEC -->|Yes| APPLY_ENC[Apply encryption standards
No secrets in localStorage]
    CHECK_SEC -->|No| CHECK_A11Y
    APPLY_ENC --> CHECK_A11Y
    CHECK_A11Y[Accessibility Validation] --> VERIFY{All MUST
requirements met?}
    VERIFY -->|Yes| CONTINUE[Continue generation]
    VERIFY -->|No| FIX[Fix to meet mandatory standards]
    FIX --> VERIFY

    style LOAD_STN fill:#fde8e8,stroke:#633
    style LOAD_BP fill:#dceefb,stroke:#336
    style LOAD_A11Y fill:#fff3cd,stroke:#856404
```

### Frontend-Relevant Enterprise Standards (MUST)

| Standard | Frontend Application |
|----------|---------------------|
| `ent-stn-iam.md` | OIDC token handling, secure redirects, session management, PKCE flow |
| `ent-stn-secrets.md` | No secrets in client bundles, use environment injection at build time |
| `ent-stn-encryption.md` | TLS-only API calls, no sensitive data in localStorage, CSP headers |
| `ent-stn-logging.md` | Structured client-side logging, no PII in console/network payloads |
| `ent-stn-devops.md` | Build pipeline standards, quality gates (lint, test, scan) |
| `arch-stn-822-iam.md` | Okta-specific: approved JS/React OIDC libraries, scope naming |

### Frontend-Relevant Best Practices (SHOULD)

| Practice | Frontend Application |
|----------|---------------------|
| `api-design.md` | Consistent error display, rate-limit awareness in UI |
| `observability.md` | Client-side telemetry, performance marks, error boundaries |
| `resiliency.md` | Retry logic on API failures, offline-first patterns, graceful degradation |
| `healthchecks.md` | Frontend health endpoint for monitoring |
| `integration-testing.md` | E2E test categories, visual regression |

---

## 11. Interaction with Other Agents

```mermaid
graph TD
    ORCH[aidlc-orchestrator
Coordinator] -->|delegates| FD[aidlc-frontend-developer
UI Code Generation]
    ORCH -->|delegates| BD[aidlc-backend-developer
APIs & Business Logic]
    ORCH -->|delegates| DB[aidlc-database-engineer
Schema & Migrations]
    ORCH -->|delegates| QA[aidlc-qa-engineer
Build & Test]
    ORCH -->|delegates| DEVOPS[aidlc-devops-engineer
Infrastructure]

    BD -.->|provides API contracts to| FD
    FD -.->|writes UI for| QA
    DEVOPS -.->|provides deploy config for| FD

    ARCH[aidlc-architect
Design Artifacts] -.->|feeds designs to| FD

    style FD fill:#61dafb,stroke:#333,color:#000
    style ORCH fill:#4a90d9,stroke:#333,color:#fff
```

| Upstream Agent | What It Provides to Frontend Developer |
|----------------|---------------------------------------|
| `aidlc-architect` | Application design, wireframes, component diagrams, UI patterns |
| `aidlc-backend-developer` | API contracts (REST/GraphQL), service interfaces, DTO shapes |
| `aidlc-devops-engineer` | CDN config, environment variables, build pipeline specs |

| Downstream Agent | What Frontend Developer Provides |
|-----------------|--------------------------------|
| `aidlc-qa-engineer` | Generated components + tests for build verification, `data-testid` map |
| `aidlc-backend-developer` | UI requirements that inform API shape (if iterating) |

---

## 12. Frontend-Specific Quality Gates

```mermaid
flowchart LR
    subgraph QUALITY["Quality Gates Applied"]
        Q1[Lint Pass
ESLint/Prettier] --> Q2[Type Check
TypeScript strict]
        Q2 --> Q3[Unit Tests
Component coverage]
        Q3 --> Q4[Accessibility Audit
axe-core / lighthouse]
        Q4 --> Q5[Bundle Size
Under budget]
        Q5 --> Q6[Visual Regression
Screenshot diff]
    end

    style QUALITY fill:#f0f4ff,stroke:#336
```

| Gate | Tool | Threshold |
|------|------|-----------|
| Lint | ESLint + Prettier | Zero errors, zero warnings |
| Types | TypeScript (strict mode) | Zero compile errors |
| Tests | Jest / Vitest + RTL | All pass, coverage per plan |
| A11y | axe-core | Zero violations (WCAG 2.1 AA) |
| Bundle | Webpack/Vite analyzer | Within defined budget |
| Visual | Chromatic / Percy | No unexpected diffs |

---

## 13. Technology Stack Awareness

The frontend developer agent adapts its output based on the project's detected stack:

```mermaid
graph TD
    FD[Frontend Developer] --> DETECT{Detect Project Stack}
    DETECT -->|React| REACT[React + JSX/TSX
Hooks, Context, Router]
    DETECT -->|Next.js| NEXT[Next.js
SSR, API routes, App Router]
    DETECT -->|Vue| VUE[Vue 3
Composition API, Pinia]
    DETECT -->|Angular| ANG[Angular
Modules, Services, RxJS]
    DETECT -->|Vanilla| VAN[HTML + CSS + JS
Web Components, ES Modules]

    REACT --> OUT[Generated Code]
    NEXT --> OUT
    VUE --> OUT
    ANG --> OUT
    VAN --> OUT

    style FD fill:#61dafb,stroke:#333,color:#000
    style OUT fill:#e8f5e9,stroke:#363
```

**Stack detection sources:**
- `package.json` → framework dependencies
- `tsconfig.json` → TypeScript configuration
- `vite.config.*` / `webpack.config.*` → build tooling
- Existing `src/` structure and file extensions

---

## 14. Completion Criteria

The frontend developer agent marks its work as **complete** when ALL of these are true:

- [x] Complete code generation plan created and approved by user
- [x] All steps in the plan marked `[x]` (no remaining `[ ]`)
- [x] All UI components generated per design specification
- [x] Responsive design implemented (mobile-first breakpoints)
- [x] Accessibility audit passed (semantic HTML, ARIA, keyboard, contrast)
- [x] `data-testid` attributes on all interactive elements
- [x] Unit/component tests generated
- [x] Build artifacts produced (if applicable)
- [x] `aidlc-state.md` updated with completion status
- [x] `journal.md` logged with approval timestamp

---

## 15. Summary

The `aidlc-frontend-developer` is a disciplined, accessibility-first, plan-driven UI code generation agent that:

1. **Never freelances** — it follows an approved plan exactly, step by step
2. **Never produces without approval** — both the plan and the output require explicit user sign-off
3. **Accessibility is non-negotiable** — WCAG 2.1 AA compliance is baked into every component via the `aidlc-accessibility-audit` skill
4. **Automation-friendly from day one** — `data-testid` attributes are added systematically using the `component-element-role` naming pattern
5. **Applies enterprise governance** — mandatory standards (MUST) and recommended patterns (SHOULD) are loaded before any code is written
6. **Generates complete UI units** — components + styles + state + API integration + tests + docs
7. **Supports brownfield and greenfield** — modifies existing UI in-place or creates new frontend structures
8. **Maintains traceability** — every generated file maps back to a user story and a plan step
9. **Framework-agnostic** — adapts output to React, Vue, Angular, Next.js, or vanilla JS based on project detection

---

*Generated: June 4, 2026 | Source: `~/.kiro/agents/aidlc-frontend-developer.json` + `aidlc-frontend-developer.md`* 