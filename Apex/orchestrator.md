​# AIDLC Orchestrator Agent — Complete Reference



> **Agent ID:** `aidlc-orchestrator`  

> **Installed In:** Apex (Kiro IDE Extension)  

> **Version:** Defined in `~/.kiro/agents/aidlc-orchestrator.json`  

> **Author:** architecture-team / aidlc-framework



---



## 1. Overview



The **aidlc-orchestrator** is the **central coordinating agent** within the AI-Driven Development Lifecycle (AI-DLC) framework. It is the only agent that interacts directly with the user by default — all other specialist agents are spawned by and report back to the orchestrator.



Its primary responsibilities are:

- Managing the three-phase lifecycle (Inception → Construction → Operations)

- Routing work to the correct specialist agent based on the current stage

- Maintaining workflow state (`aidlc-state.md`)

- Enforcing governance gates and stage transitions

- Logging all decisions and transitions to `journal.md`

- Handling session continuity (pause, resume, skip)



The orchestrator is **invoked directly by the user** — it is the entry point to the AI-DLC workflow. When a user says "start the AIDLC" or triggers a spec-based workflow, the orchestrator takes control.



---



## 2. Agent Configuration



```json

{

  "name": "aidlc-orchestrator",

  "description": "AI-DLC workflow orchestrator. Routes work to specialist agents based on the current AI-DLC phase and stage.",

  "prompt": "file://./aidlc-orchestrator.md",

  "tools": ["read", "write", "shell", "web", "spec"],

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

| `tools` | `read`, `write`, `shell`, `web`, `spec` | Full tool access — reads files, writes state/docs, runs commands, accesses web, manages specs |

| `allowedTools` | `read` | Auto-approved without user confirmation |

| `resources` | Workspace + user steering & skills | Access to all enterprise rules, project context, and skill definitions |



---



## 3. Role & Responsibilities



```

┌──────────────────────────────────────────────────────────────────┐

│                    AIDLC Orchestrator                             │

├──────────────────────────────────────────────────────────────────┤

│ • Entry point for all AI-DLC workflow sessions                   │

│ • Present welcome message and collect session objective          │

│   (Explore vs Deliver)                                           │

│ • Determine governance mode (Active vs Shadow)                   │

│ • Manage workspace detection (greenfield/brownfield assessment)  │

│ • Route work to specialist agents via delegation matrix          │

│ • Enforce stage-gate transitions (plan→validate→execute cycle)   │

│ • Maintain aidlc-state.md (current phase, stage, progress)       │

│ • Write all decisions and transitions to journal.md              │

│ • Handle session continuity (pause/resume/skip)                  │

│ • Coordinate governance checks across stages                     │

│ • Present aggregated results from specialist agents to user      │

│ • Manage opt-in rule activation during requirements stage        │

│ • Enforce artefact location rules (code vs docs separation)      │

│ • Handle mid-workflow changes (objective switch, mode switch)     │

│ • Process user overrides at governance gates                     │

│ • Coordinate multi-unit construction (sequential unit execution) │

│ • Compile Operational Readiness Pack (ORP) in operations phase   │

└──────────────────────────────────────────────────────────────────┘

```



---



## 4. Position in the AI-DLC Lifecycle



```mermaid

flowchart TB

    subgraph INCEPTION["Phase 1: INCEPTION"]

        direction LR

        WD[Workspace Detection] --> RA[Requirements Analysis]

        RA --> GT[Governance Triage]

        GT --> US[User Stories]

        US --> WP[Workflow Planning]

        WP --> AD[Application Design]

        AD --> UG[Units Generation]

        UG --> REG[Ready for Engineering Gate]

    end



    subgraph CONSTRUCTION["Phase 2: CONSTRUCTION (per unit)"]

        direction LR

        FD[Functional Design] --> NFR[NFR Design]

        NFR --> ID[Infrastructure Design]

        ID --> CG[Code Generation]

        CG --> BT[Build & Test]

    end



    subgraph OPERATIONS["Phase 3: OPERATIONS"]

        direction LR

        DV[Delivery Verification] --> DAG[Design Approved Gate]

        DAG --> ORP[Operational Readiness Pack]

    end



    REG --> FD

    BT --> DV



    ORCH[aidlc-orchestrator<br/>COORDINATES ALL STAGES]

    ORCH -.->|manages| INCEPTION

    ORCH -.->|manages| CONSTRUCTION

    ORCH -.->|manages| OPERATIONS



    style ORCH fill:#4a90d9,stroke:#333,color:#fff,stroke-width:3px

    style INCEPTION fill:#e8f5e9,stroke:#2e7d32

    style CONSTRUCTION fill:#e3f2fd,stroke:#1565c0

    style OPERATIONS fill:#fce4ec,stroke:#c62828

```



> The **Orchestrator** sits above all three phases. It does not execute the work itself — it coordinates, delegates, validates, and records.



---



## 5. Core Workflow — The Plan→Validate→Execute Cycle



```mermaid

flowchart TD

    START[User Request / Session Start] --> DETECT[Workspace Detection]

    DETECT --> SESSION[Collect Session Objective<br/>Explore or Deliver]

    SESSION --> GOV_MODE[Collect Governance Mode<br/>Active or Shadow]

    GOV_MODE --> ASSESS{Existing Artefacts<br/>Provided?}

    ASSESS -->|Yes| ENTRY[Assess & Recommend<br/>Entry Point]

    ASSESS -->|No| STAGE1[Start at Requirements Analysis]

    ENTRY --> STAGE1



    STAGE1 --> DELEGATE[Delegate to Specialist Agent]

    DELEGATE --> PLAN[Specialist Creates Plan/Output]

    PLAN --> PRESENT[Orchestrator Presents to User]

    PRESENT --> VALIDATE{User Approves?}

    VALIDATE -->|Yes| RECORD[Record in Journal + State]

    VALIDATE -->|No| REVISE[Specialist Revises]

    REVISE --> PRESENT

    RECORD --> NEXT{More Stages?}

    NEXT -->|Yes| GATE{Gate Check<br/>Required?}

    NEXT -->|No| COMPLETE[Workflow Complete]

    GATE -->|Pass| DELEGATE

    GATE -->|Fail| RESOLVE[Resolve Gate Issues]

    RESOLVE --> GATE



    style START fill:#4a90d9,stroke:#333,color:#fff

    style COMPLETE fill:#4caf50,stroke:#333,color:#fff

    style DELEGATE fill:#ff9800,stroke:#333,color:#fff

```



---



## 6. Delegation Matrix — Agent Routing



The orchestrator uses a delegation matrix to determine which specialist agent handles each stage:



```mermaid

graph TD

    ORCH[aidlc-orchestrator] -->|Inception: Requirements| RA[aidlc-requirements-analyst]

    ORCH -->|Inception: User Stories| PO[aidlc-product-owner]

    ORCH -->|Inception: Application Design| ARCH[aidlc-architect]

    ORCH -->|Construction: Code Gen — Frontend| FE[aidlc-frontend-developer]

    ORCH -->|Construction: Code Gen — Backend| BE[aidlc-backend-developer]

    ORCH -->|Construction: Code Gen — Database| DBE[aidlc-database-engineer]

    ORCH -->|Construction: Infrastructure| DEVOPS[aidlc-devops-engineer]

    ORCH -->|Construction: Build & Test| QA[aidlc-qa-engineer]

    ORCH -->|Operations: Security Review| SEC[aidlc-security-engineer]

    ORCH -->|Operations: SRE & Monitoring| SRE[aidlc-sre-engineer]

    ORCH -->|Any Stage: Documentation| TW[aidlc-technical-writer]

    ORCH -->|Any Stage: Jira Integration| JW[aidlc-jira-writer]

    ORCH -->|Any Stage: Doc Writing| DW[aidlc-doc-writer]



    style ORCH fill:#4a90d9,stroke:#333,color:#fff,stroke-width:3px

    style RA fill:#ab47bc,stroke:#333,color:#fff

    style PO fill:#7e57c2,stroke:#333,color:#fff

    style ARCH fill:#5c6bc0,stroke:#333,color:#fff

    style FE fill:#61dafb,stroke:#333,color:#000

    style BE fill:#68d391,stroke:#333,color:#000

    style DBE fill:#f6ad55,stroke:#333,color:#000

    style DEVOPS fill:#fc8181,stroke:#333,color:#000

    style QA fill:#4fd1c5,stroke:#333,color:#000

    style SEC fill:#f56565,stroke:#333,color:#fff

    style SRE fill:#ed8936,stroke:#333,color:#000

    style TW fill:#a0aec0,stroke:#333,color:#000

    style JW fill:#63b3ed,stroke:#333,color:#000

    style DW fill:#b794f4,stroke:#333,color:#000

```



### Delegation Decision Logic



| Stage | Condition | Agent Delegated To |

|-------|-----------|-------------------|

| Requirements Analysis | Always | `aidlc-requirements-analyst` |

| User Stories | Conditional (not trivial changes) | `aidlc-product-owner` |

| Application Design | Conditional (material changes) | `aidlc-architect` |

| Units Generation | Conditional (multi-component work) | `aidlc-architect` |

| Functional Design | Conditional (complex logic) | `aidlc-architect` |

| NFR Design | Conditional (NFRs identified) | `aidlc-architect` |

| Infrastructure Design | Always | `aidlc-devops-engineer` |

| Code Generation — Frontend | Unit is frontend-scoped | `aidlc-frontend-developer` |

| Code Generation — Backend | Unit is backend-scoped | `aidlc-backend-developer` |

| Code Generation — Database | Unit is data-layer-scoped | `aidlc-database-engineer` |

| Build & Test | Always (after all units) | `aidlc-qa-engineer` |

| Security Review | On-demand or governance-triggered | `aidlc-security-engineer` |

| Delivery Verification | Always | Orchestrator (internal) |

| Operational Readiness Pack | Always | Orchestrator + `aidlc-sre-engineer` |



---



## 7. Skills & Steering Files Used



### 7.1 Skills Loaded at Runtime



```mermaid

graph TD

    ORCH[aidlc-orchestrator] --> WORKFLOW[aidlc-workflow<br/>Lifecycle Workflow Rules]

    ORCH --> RULES[aidlc-rules<br/>Stage Rules & Governance]

    ORCH --> GUIDE[aidlc-guide<br/>Process Explanations]

    ORCH --> ENT_STN[aidlc-ent-stn<br/>Enterprise Standards]

    ORCH --> ENT_BP[aidlc-ent-bp<br/>Enterprise Best Practices]



    WORKFLOW --> WF1[Three-Phase Lifecycle<br/>Inception → Construction → Operations]

    WORKFLOW --> WF2[Core Pattern<br/>Plan → Validate → Execute]

    WORKFLOW --> WF3[Session Continuity<br/>Pause/Resume/Skip]

    WORKFLOW --> WF4[State Management<br/>aidlc-state.md + journal.md]



    RULES --> R_COMMON[Common Rules<br/>Always loaded at start]

    RULES --> R_INCEPTION[Inception Stage Rules]

    RULES --> R_CONSTRUCTION[Construction Stage Rules]

    RULES --> R_OPERATIONS[Operations Stage Rules]

    RULES --> R_OPTIN[Opt-In Governance Rules]



    GUIDE --> G1[Process Questions<br/>"How does this work?"]

    GUIDE --> G2[Stage Explanations<br/>"What stage am I at?"]

    GUIDE --> G3[Governance Help<br/>"What governance applies?"]



    ENT_STN --> S1[IAM / Okta / OIDC]

    ENT_STN --> S2[Secrets Management]

    ENT_STN --> S3[Encryption Standards]

    ENT_STN --> S4[Logging & Monitoring]

    ENT_STN --> S5[Environment Separation]

    ENT_STN --> S6[CI/CD Standards]

    ENT_STN --> S7[AWS Cloud Architecture]



    ENT_BP --> B1[API Design]

    ENT_BP --> B2[Observability]

    ENT_BP --> B3[Resiliency]

    ENT_BP --> B4[Health Checks]

    ENT_BP --> B5[Integration Testing]

    ENT_BP --> B6[Container Security]



    style ORCH fill:#4a90d9,stroke:#333,color:#fff,stroke-width:3px

    style WORKFLOW fill:#c8e6c9,stroke:#2e7d32

    style RULES fill:#e8f5e9,stroke:#2e7d32

    style GUIDE fill:#fff9c4,stroke:#f57f17

    style ENT_STN fill:#fde8e8,stroke:#633

    style ENT_BP fill:#dceefb,stroke:#336

```



### 7.2 Skill Detail Table



| Skill | Type | When Loaded | Purpose |

|-------|------|-------------|---------|

| `aidlc-workflow` | Core Workflow | Session start (always) | Defines the three-phase lifecycle, core plan→validate→execute pattern, state management, and session continuity rules |

| `aidlc-rules` | Stage Rules | Per-stage (progressive) | Stage-specific rules loaded on demand: workspace-detection, requirements-analysis, governance-triage, code-generation, etc. Includes opt-in governance rule definitions |

| `aidlc-guide` | Process Guide | On user questions | Answers process/governance questions conversationally — "What stage am I at?", "What is an ADB?", "Can I skip this?" |

| `aidlc-ent-stn` | Enterprise Standards (MUST) | At governance checks & design stages | Mandatory TP ICAP architecture standards — IAM, secrets, encryption, logging, CI/CD, STN-822, STN-828, AWS cloud |

| `aidlc-ent-bp` | Enterprise Best Practices (SHOULD) | At design & code generation stages | Recommended patterns — API design, observability, resiliency, health checks, containers, testing |



### 7.3 Additional Skills Available for Delegation



The orchestrator does not load these directly but ensures they are available to specialist agents:



| Skill | Used By | Purpose |

|-------|---------|---------|

| `aidlc-accessibility-audit` | `aidlc-frontend-developer`, `aidlc-qa-engineer` | WCAG 2.1 AA compliance validation |

| `aidlc-adr-writing` | `aidlc-architect` | Architecture Decision Record templates and patterns |

| `aidlc-ddd-modeling` | `aidlc-architect` | Domain-Driven Design patterns (aggregates, bounded contexts) |

| `aidlc-doc-writing` | `aidlc-doc-writer`, `aidlc-technical-writer` | Documentation templates and style guide |

| `aidlc-jira-writing` | `aidlc-jira-writer` | Jira ticket templates (stories, bugs, tasks, epics) |



### 7.4 Steering Files Accessible



The orchestrator has access to all steering files and passes relevant context to delegated agents:



| Scope | Path Pattern | Content |

|-------|-------------|---------|

| Workspace | `.kiro/steering/**/*.md` | Project-specific context (tech stack, structure, product domain) |

| User/Global | `~/.kiro/steering/**/*.md` | Cross-project enterprise rules |



### 7.5 Rules Reference Files (Progressive Disclosure)



The orchestrator loads rules progressively — only the rules relevant to the current stage are loaded to conserve context:



```mermaid

graph LR

    subgraph COMMON["Always Loaded"]

        C1[process-overview.md]

        C2[session-continuity.md]

        C3[content-validation.md]

        C4[question-format-guide.md]

        C5[aidlc-state-template.md]

        C6[terminology.md]

        C7[depth-levels.md]

        C8[error-handling.md]

        C9[overconfidence-prevention.md]

        C10[workflow-changes.md]

        C11[welcome-message.md]

        C12[ascii-diagram-standards.md]

    end



    subgraph INCEPTION_RULES["Loaded During Inception"]

        I1[workspace-detection.md]

        I2[requirements-analysis.md]

        I3[governance-triage.md]

        I4[user-stories.md]

        I5[workflow-planning.md]

        I6[application-design.md]

        I7[units-generation.md]

        I8[reverse-engineering.md]

    end



    subgraph CONSTRUCTION_RULES["Loaded During Construction"]

        CON1[functional-design.md]

        CON2[nfr-requirements.md]

        CON3[nfr-design.md]

        CON4[infrastructure-design.md]

        CON5[code-generation.md]

        CON6[build-and-test.md]

    end



    subgraph OPERATIONS_RULES["Loaded During Operations"]

        O1[operations.md]

    end



    subgraph OPTIN["Opt-In Rules (Activated Conditionally)"]

        OP1[adb/ — Architecture Design Brief]

        OP2[ads/ — Architecture Design Summary]

        OP3[build-path/ — Prototype vs Enterprise]

        OP4[delivery-verification/ — Code vs Design]

        OP5[operational-readiness-pack/ — ORP]

        OP6[standards-check/ — ADR Standards<br/>ALWAYS ON]

        OP7[security-review/ — Security Posture]

        OP8[ai-compliance/ — AI Impact Assessment]

        OP9[test-evidence/ — Test Evidence + RCA]

        OP10[jira-sync/ — Jira Integration]

        OP11[security-baseline/ — Security Baseline]

        OP12[property-based-testing/]

        OP13[template-baseline/]

    end



    style COMMON fill:#e8f5e9,stroke:#2e7d32

    style INCEPTION_RULES fill:#fff9c4,stroke:#f57f17

    style CONSTRUCTION_RULES fill:#e3f2fd,stroke:#1565c0

    style OPERATIONS_RULES fill:#fce4ec,stroke:#c62828

    style OPTIN fill:#f3e5f5,stroke:#7b1fa2

```



---



## 8. Governance Management



### 8.1 Governance Modes



```mermaid

stateDiagram-v2

    [*] --> SessionStart

    SessionStart --> Active: User chooses Active

    SessionStart --> Shadow: User chooses Shadow



    Active --> Shadow: "Switch to shadow"

    Shadow --> Active: "Switch to active"



    state Active {

        [*] --> CheckRuns

        CheckRuns --> FindingsPresented: Findings exist

        CheckRuns --> SilentPass: No findings

        FindingsPresented --> UserAcknowledges

        UserAcknowledges --> NextStage

        SilentPass --> NextStage

    }



    state Shadow {

        [*] --> CheckRunsS

        CheckRunsS --> LoggedSilently: Findings logged to<br/>governance-shadow-log.md

        CheckRunsS --> SilentPassS: No findings

        LoggedSilently --> NextStageS

        SilentPassS --> NextStageS

    }

```



### 8.2 Governance Gates Managed



| Gate | When Checked | Blocking in Deliver? |

|------|-------------|---------------------|

| **CMF** (Change Management Forum) | Governance Triage | Advisory |

| **Technology Triage** | Governance Triage | Advisory |

| **Procurement / IRM** | Governance Triage (flagged early) | Can block if vendor-dependent |

| **SAF** (Solution Authority Forum) | Application Design / Ready for Engineering Gate | Yes — design must be approved |

| **AI Governance** | Governance Triage (if AI/ML in scope) | Yes |

| **Standards-Check** | Every ADR written (always-on) | Advisory in Explore, directive in Deliver |

| **PtO** (Permit to Operate) | Operations phase | Yes — must pass before go-live |

| **CAB** (Change Approval Board) | Operations phase | Yes — must pass before production |



### 8.3 Always-Flagged Categories (Even in Explore Mode)



```mermaid

graph LR

    ALWAYS[Always Flagged<br/>Regardless of Mode] --> V[Vendor Dependencies]

    ALWAYS --> RD[Regulated Data]

    ALWAYS --> AI[AI / ML Solutions]

    ALWAYS --> RJ[Region / Jurisdiction Changes]

    ALWAYS --> RT[Regulated Trading]



    style ALWAYS fill:#f44336,stroke:#333,color:#fff

    style V fill:#ffcdd2,stroke:#c62828

    style RD fill:#ffcdd2,stroke:#c62828

    style AI fill:#ffcdd2,stroke:#c62828

    style RJ fill:#ffcdd2,stroke:#c62828

    style RT fill:#ffcdd2,stroke:#c62828

```



---



## 9. State Management



### 9.1 State File — `aidlc-docs/aidlc-state.md`



The orchestrator maintains a single state file that tracks the entire session:



```mermaid

graph TD

    STATE[aidlc-state.md] --> CURR[Current Stage & Phase]

    STATE --> OBJ[Session Objective<br/>Explore / Deliver]

    STATE --> GOV[Governance Configuration<br/>Active / Shadow]

    STATE --> TRIAGE[Governance Triage Results<br/>Applicable gates]

    STATE --> PROGRESS[Stage Progress<br/>Completed / In-Progress / Remaining]

    STATE --> UNITS[Units of Work<br/>Name, scope, status]

    STATE --> OPTIN_STATE[Opt-In Rules<br/>Activated / Deactivated]

    STATE --> ARTEFACTS[Artefact Registry<br/>Files produced per stage]

    STATE --> OVERRIDES[Recorded Overrides<br/>Gates skipped with reason]



    style STATE fill:#4a90d9,stroke:#333,color:#fff,stroke-width:2px

```



### 9.2 Journal — `aidlc-docs/journal.md`



Every significant event is logged:



| Event Type | What's Recorded |

|-----------|-----------------|

| Stage transition | From → To, timestamp, reason |

| Delegation | Agent spawned, context provided |

| User approval | What was approved, timestamp |

| Governance finding | Category, severity, recommendation |

| Override | What was overridden, user's reason |

| Session pause/resume | State at pause, state at resume |

| Opt-in activation | Rule activated, trigger |

| Skip | What was skipped, downstream implications |



---



## 10. Session Lifecycle Management



```mermaid

sequenceDiagram

    participant User

    participant ORCH as aidlc-orchestrator

    participant State as aidlc-state.md

    participant Journal as journal.md

    participant Specialist as Specialist Agent



    Note over User,ORCH: SESSION START

    User->>ORCH: "Start AIDLC" / trigger workflow

    ORCH->>ORCH: Load aidlc-workflow skill

    ORCH->>ORCH: Load aidlc-rules (common)

    ORCH->>ORCH: Check for existing aidlc-state.md



    alt Resuming Session

        ORCH->>State: Read current state

        ORCH->>User: "Welcome back. You were at [stage].<br/>Resume / Skip / Review?"

    else New Session

        ORCH->>User: Welcome message + collect objective & mode

        User->>ORCH: "Deliver" + "Active"

        ORCH->>State: Create from template

        ORCH->>Journal: Log session start

    end



    Note over User,ORCH: STAGE EXECUTION

    ORCH->>ORCH: Load stage-specific rules

    ORCH->>Specialist: Delegate stage work

    Specialist->>User: Present output for validation

    User->>Specialist: Approve / Request changes

    Specialist->>ORCH: Return results

    ORCH->>State: Update progress

    ORCH->>Journal: Log completion + approval



    Note over User,ORCH: GATE CHECK

    ORCH->>ORCH: Check governance gates

    ORCH->>User: Present findings (if Active mode)

    User->>ORCH: Acknowledge / Override

    ORCH->>Journal: Log governance decision



    Note over User,ORCH: SESSION PAUSE

    User->>ORCH: "Let's pause"

    ORCH->>State: Save full state

    ORCH->>Journal: Log pause

    ORCH->>User: "Saved. Resume anytime."

```



---



## 11. Delegation Pattern — How Work Is Routed



```mermaid

flowchart TD

    TRIGGER[Stage Reached] --> CHECK_COND{Stage<br/>Conditional?}

    CHECK_COND -->|No — Always runs| LOAD_RULES[Load Stage Rules]

    CHECK_COND -->|Yes| EVAL_COND{Condition<br/>Met?}

    EVAL_COND -->|No| SKIP[Skip Stage<br/>Log in Journal]

    EVAL_COND -->|Yes| LOAD_RULES



    LOAD_RULES --> IDENTIFY[Identify Target Agent<br/>from Delegation Matrix]

    IDENTIFY --> CONTEXT[Prepare Context Package<br/>• Design artifacts<br/>• Requirements<br/>• Steering files<br/>• Previous stage output]

    CONTEXT --> SPAWN[Spawn Specialist Agent<br/>with context + instructions]

    SPAWN --> MONITOR[Monitor Execution<br/>• Check for errors<br/>• Handle timeouts]

    MONITOR --> RECEIVE[Receive Results]

    RECEIVE --> VALIDATE[Present to User<br/>for Approval]

    VALIDATE --> PERSIST[Persist Artifacts<br/>Update State & Journal]

    PERSIST --> NEXT[Advance to Next Stage]



    style TRIGGER fill:#4a90d9,stroke:#333,color:#fff

    style SPAWN fill:#ff9800,stroke:#333,color:#fff

    style PERSIST fill:#4caf50,stroke:#333,color:#fff

```



---



## 12. Adaptive Depth Levels



The orchestrator adjusts workflow depth based on the complexity of the user's request:



```mermaid

graph TD

    REQUEST[User Request] --> ANALYZE{Analyze Complexity}

    ANALYZE -->|Trivial| L1[Level 1: Minimal<br/>Skip most inception stages<br/>Direct to code generation]

    ANALYZE -->|Simple| L2[Level 2: Light<br/>Quick requirements<br/>Minimal design → code]

    ANALYZE -->|Standard| L3[Level 3: Standard<br/>Full inception<br/>Full construction per unit]

    ANALYZE -->|Complex| L4[Level 4: Deep<br/>Extended requirements rounds<br/>Multiple design iterations<br/>Multi-unit coordination]

    ANALYZE -->|Enterprise| L5[Level 5: Full Governance<br/>All gates active<br/>ADB/ADS required<br/>SAF review<br/>Full ORP]



    L1 --> OUTPUT1["Stages: Code Gen → Build"]

    L2 --> OUTPUT2["Stages: Req → Design → Code → Build"]

    L3 --> OUTPUT3["Stages: All Inception → All Construction → Ops"]

    L4 --> OUTPUT4["Stages: All + Multiple Q&A rounds + Multi-unit"]

    L5 --> OUTPUT5["Stages: All + Governance gates + External reviews"]



    style L1 fill:#c8e6c9,stroke:#2e7d32

    style L2 fill:#dcedc8,stroke:#33691e

    style L3 fill:#fff9c4,stroke:#f57f17

    style L4 fill:#ffe0b2,stroke:#e65100

    style L5 fill:#ffcdd2,stroke:#c62828

```



---



## 13. Multi-Unit Construction Coordination



When the architect produces multiple units of work, the orchestrator sequences their execution:



```mermaid

flowchart TD

    UNITS[Units Generated by Architect] --> ORDER[Determine Execution Order<br/>Based on dependencies]

    ORDER --> U1[Unit 1: Database Layer]

    ORDER --> U2[Unit 2: Backend API]

    ORDER --> U3[Unit 3: Frontend UI]



    U1 --> FD1[Functional Design] --> NFR1[NFR Design] --> ID1[Infra Design] --> CG1[Code Gen<br/>→ database-engineer]

    CG1 --> DONE1[Unit 1 Complete]



    U2 --> FD2[Functional Design] --> NFR2[NFR Design] --> ID2[Infra Design] --> CG2[Code Gen<br/>→ backend-developer]

    CG2 --> DONE2[Unit 2 Complete]



    U3 --> FD3[Functional Design] --> NFR3[NFR Design] --> ID3[Infra Design] --> CG3[Code Gen<br/>→ frontend-developer]

    CG3 --> DONE3[Unit 3 Complete]



    DONE1 --> BT

    DONE2 --> BT

    DONE3 --> BT[Build & Test<br/>→ qa-engineer]



    BT --> OPS[Operations Phase]



    style U1 fill:#f6ad55,stroke:#333

    style U2 fill:#68d391,stroke:#333

    style U3 fill:#61dafb,stroke:#333

    style BT fill:#4fd1c5,stroke:#333

```



---



## 14. Interaction with All Other Agents



```mermaid

graph TD

    ORCH[aidlc-orchestrator<br/>CENTRAL COORDINATOR]



    subgraph INCEPTION_AGENTS["Inception Phase Agents"]

        RA[requirements-analyst<br/>Requirements Gathering]

        PO[product-owner<br/>User Stories & Personas]

        ARCH[architect<br/>Design & ADRs]

    end



    subgraph CONSTRUCTION_AGENTS["Construction Phase Agents"]

        FE[frontend-developer<br/>UI Code]

        BE[backend-developer<br/>API & Logic]

        DBE[database-engineer<br/>Schema & Queries]

        DEVOPS[devops-engineer<br/>IaC & Pipelines]

        QA[qa-engineer<br/>Testing]

    end



    subgraph OPERATIONS_AGENTS["Operations Phase Agents"]

        SEC[security-engineer<br/>Security Review]

        SRE[sre-engineer<br/>Monitoring & SRE]

    end



    subgraph CROSS_CUTTING["Cross-Cutting Agents"]

        TW[technical-writer<br/>Documentation]

        DW[doc-writer<br/>Confluence Docs]

        JW[jira-writer<br/>Jira Tickets]

    end



    ORCH ==>|delegates| RA

    ORCH ==>|delegates| PO

    ORCH ==>|delegates| ARCH

    ORCH ==>|delegates| FE

    ORCH ==>|delegates| BE

    ORCH ==>|delegates| DBE

    ORCH ==>|delegates| DEVOPS

    ORCH ==>|delegates| QA

    ORCH ==>|delegates| SEC

    ORCH ==>|delegates| SRE

    ORCH ==>|delegates| TW

    ORCH ==>|delegates| DW

    ORCH ==>|delegates| JW



    RA -.->|feeds| PO

    PO -.->|feeds| ARCH

    ARCH -.->|feeds| FE

    ARCH -.->|feeds| BE

    ARCH -.->|feeds| DBE

    ARCH -.->|feeds| DEVOPS

    BE -.->|API contracts| FE

    DBE -.->|schemas| BE

    FE -.->|UI + tests| QA

    BE -.->|services + tests| QA

    QA -.->|evidence| SRE



    style ORCH fill:#4a90d9,stroke:#333,color:#fff,stroke-width:3px

    style INCEPTION_AGENTS fill:#e8f5e9,stroke:#2e7d32

    style CONSTRUCTION_AGENTS fill:#e3f2fd,stroke:#1565c0

    style OPERATIONS_AGENTS fill:#fce4ec,stroke:#c62828

    style CROSS_CUTTING fill:#f3e5f5,stroke:#7b1fa2

```



---



## 15. Session Objective Comparison



```mermaid

graph LR

    subgraph EXPLORE["Explore Mode"]

        E1[Governance = suggestions only]

        E2[No ADB/ADS required]

        E3[No gates between phases]

        E4[Move freely between stages]

        E5[5 always-flagged categories<br/>still apply]

    end



    subgraph DELIVER["Deliver Mode"]

        D1[Governance = can block]

        D2[ADB/ADS required if material]

        D3[Gates between phases enforced]

        D4[Sequential stage progression]

        D5[Full evidence trail for ORP]

    end



    style EXPLORE fill:#c8e6c9,stroke:#2e7d32

    style DELIVER fill:#ffcdd2,stroke:#c62828

```



| Aspect | Explore | Deliver |

|--------|---------|---------|

| Purpose | Prototyping, validation, PoC | Production-bound development |

| Governance findings | Suggestions — non-blocking | Advise, direct, or block based on severity |

| Design documents | Not required | Required if change is material |

| Stage gates | None — free movement | Enforced (design review, verification) |

| ORP generation | Not generated | Generated in operations phase |

| Switch allowed? | Yes, at any time | Yes, at any time |



---



## 16. Critical Rules & Constraints



```mermaid

graph LR

    subgraph IMMUTABLE["Immutable Rules — Never Violate"]

        R1["Never skip plan→validate→execute cycle"]

        R2["All questions in .md files, never chat-only"]

        R3["Log EVERYTHING in journal.md"]

        R4["Update aidlc-state.md after every transition"]

        R5["Application code → workspace root ONLY"]

        R6["Documentation → aidlc-docs/ ONLY"]

        R7["Use aidlc-state-template.md — never create from scratch"]

        R8["Progressive rule loading — never load all rules at once"]

    end



    subgraph DELEGATION["Delegation Rules"]

        D1["Never execute specialist work directly"]

        D2["Always provide full context when delegating"]

        D3["Record delegation in journal"]

        D4["Validate specialist output before presenting to user"]

        D5["Handle specialist errors gracefully"]

    end



    subgraph GOVERNANCE_RULES["Governance Rules"]

        G1["Standards-check is ALWAYS ON"]

        G2["5 categories always flagged (even Explore)"]

        G3["Overrides require user reason — record it"]

        G4["Shadow mode logs findings, doesn't suppress checks"]

        G5["Gate failures in Deliver mode block progression"]

    end



    style IMMUTABLE fill:#fff3cd,stroke:#856404

    style DELEGATION fill:#dceefb,stroke:#336

    style GOVERNANCE_RULES fill:#fde8e8,stroke:#633

```



---



## 17. Error Recovery & Edge Cases



| Scenario | Orchestrator Response |

|----------|----------------------|

| Specialist agent fails | Log error → retry once → if still fails, present error to user with options |

| User provides existing artefacts | Assess coverage → recommend entry point → allow skip/start-fresh |

| Mid-session objective switch | Update state → recalculate applicable governance → inform user of implications |

| User requests skip | Allow → log skip → inform of downstream checks that will still run |

| Session context compacted | Re-read `aidlc-state.md` → confirm position → continue from last recorded state |

| Conflicting requirements | Flag contradiction → ask user to resolve → record decision |

| Governance check fails in Shadow mode | Log silently → continue → present summary if user asks |



---



## 18. Artefact Registry — What Gets Produced



```mermaid

graph TD

    subgraph INCEPTION_ARTEFACTS["Inception Phase Artefacts"]

        IA1[requirements.md]

        IA2[governance-triage.md]

        IA3[user-stories.md]

        IA4[workflow-plan.md]

        IA5[application-design.md]

        IA6[ADRs — adr-001.md, adr-002.md...]

        IA7[units-of-work.md]

        IA8[ADB.md — if required]

    end



    subgraph CONSTRUCTION_ARTEFACTS["Construction Phase Artefacts"]

        CA1[functional-design.md — per unit]

        CA2[nfr-design.md — per unit]

        CA3[infrastructure-design.md — per unit]

        CA4[code-generation-plan.md — per unit]

        CA5[Generated Source Code]

        CA6[Test Results / Evidence]

    end



    subgraph OPERATIONS_ARTEFACTS["Operations Phase Artefacts"]

        OA1[delivery-verification.md]

        OA2[security-review.md]

        OA3[operational-readiness-pack.md]

        OA4[governance-shadow-log.md — if Shadow mode]

    end



    subgraph ALWAYS["Always Maintained"]

        AL1[aidlc-state.md]

        AL2[journal.md]

    end



    style INCEPTION_ARTEFACTS fill:#e8f5e9,stroke:#2e7d32

    style CONSTRUCTION_ARTEFACTS fill:#e3f2fd,stroke:#1565c0

    style OPERATIONS_ARTEFACTS fill:#fce4ec,stroke:#c62828

    style ALWAYS fill:#4a90d9,stroke:#333,color:#fff

```



All artefacts are stored under `aidlc-docs/` with this structure:

```

aidlc-docs/

├── aidlc-state.md

├── journal.md

├── inception/

│   ├── requirements.md

│   ├── governance-triage.md

│   ├── user-stories.md

│   ├── workflow-plan.md

│   ├── application-design.md

│   ├── adrs/

│   │   ├── adr-001.md

│   │   └── ...

│   └── units-of-work.md

├── construction/

│   ├── plans/

│   │   ├── {unit}-code-generation-plan.md

│   │   └── ...

│   ├── designs/

│   │   ├── {unit}-functional-design.md

│   │   ├── {unit}-nfr-design.md

│   │   └── {unit}-infrastructure-design.md

│   └── evidence/

│       └── test-results.md

└── operations/

    ├── delivery-verification.md

    ├── security-review.md

    └── operational-readiness-pack.md

```



---



## 19. Comparison with Other Agents



| Aspect | Orchestrator | Specialist Agents |

|--------|-------------|-------------------|

| User interaction | **Direct** — primary point of contact | Indirect — spawned by orchestrator |

| Scope | Entire lifecycle | Single stage or skill |

| State management | **Owns** `aidlc-state.md` and `journal.md` | Reports back to orchestrator |

| Governance | **Enforces** gates and checks | Applies standards within their scope |

| Delegation | **Delegates** work to others | Receives delegated work |

| Tools | Full suite (`read`, `write`, `shell`, `web`, `spec`) | Typically `read`, `write`, `shell` |

| Skills loaded | Workflow + Rules + Guide + Enterprise | Stage-specific + Enterprise |

| Lifecycle awareness | Full (all 3 phases) | Stage-local only |



---



## 20. Summary



The `aidlc-orchestrator` is the **brain of the AI-DLC framework** — it:



1. **Coordinates everything** — no specialist agent acts without orchestrator delegation

2. **Maintains the single source of truth** — `aidlc-state.md` tracks every decision, `journal.md` logs every event

3. **Enforces the plan→validate→execute cycle** — nothing ships without user approval at every stage

4. **Manages governance intelligently** — adapts between Explore (advisory) and Deliver (blocking), with 5 always-flagged categories that cannot be bypassed

5. **Routes work precisely** — uses a delegation matrix to spawn the exact right specialist for each stage

6. **Handles session continuity** — pause, resume, skip, switch modes — all without losing context

7. **Loads rules progressively** — only the rules for the current stage are loaded, conserving context window

8. **Produces a complete evidence trail** — from requirements through design through code through deployment verification, all traceable and auditable

9. **Supports adaptive depth** — a one-line bug fix gets Level 1 (direct code gen); an enterprise platform gets Level 5 (full governance)

10. **Never executes specialist work directly** — it coordinates, it does not implement



The orchestrator is what makes the AI-DLC a **governed, traceable, resumable workflow** rather than just a collection of independent AI agents.



---



*Generated: June 4, 2026 | Source: `~/.kiro/agents/aidlc-orchestrator.json` + `aidlc-orchestrator.md` + activated skills: `aidlc-workflow`, `aidlc-rules`, `aidlc-guide`*

