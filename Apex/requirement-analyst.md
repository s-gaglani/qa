# AIDLC Requirements Analyst Agent — Complete Reference

m

> **Agent ID:** `aidlc-requirements-analyst`  

> **Installed In:** Apex (Kiro IDE Extension)  

> **Version:** Defined in `~/.kiro/agents/aidlc-requirements-analyst.json`  

> **Author:** architecture-team / aidlc-framework



---



## 1. Overview



The **aidlc-requirements-analyst** is a specialist sub-agent within the AI-Driven Development Lifecycle (AI-DLC) framework. It is responsible for gathering, analysing, validating, and structuring requirements during the **Inception Phase → Requirements Analysis** stage.



Unlike construction-phase agents (backend, frontend, devops), the requirements analyst operates at the **very beginning** of the lifecycle — it is the first substantive stage after Workspace Detection. Its output shapes every downstream decision: user stories, architecture design, code generation, and governance classification.



It is **never invoked directly by the user**. The **aidlc-orchestrator** agent delegates work to it when the workflow reaches the Requirements Analysis stage.



---



## 2. Agent Configuration



```json

{

  "name": "aidlc-requirements-analyst",

  "description": "AI-DLC requirements analyst for requirements gathering, intent analysis, clarifying questions, and NFR identification.",

  "prompt": "file://./aidlc-requirements-analyst.md",

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

| `tools` | `read`, `write`, `shell` | Can read files, write questions/docs, run shell for discovery |

| `allowedTools` | `read` | Auto-approved without user confirmation |

| `resources` | Workspace + user steering & skills | Access to all enterprise rules and project context |



---



## 3. Role & Responsibilities



```

┌─────────────────────────────────────────────────────┐

│          AIDLC Requirements Analyst                  │

├─────────────────────────────────────────────────────┤

│ • Analyse user intent from initial request          │

│ • Generate structured clarifying questions          │

│ • Identify functional requirements (FRs)            │

│ • Identify non-functional requirements (NFRs)       │

│ • Detect ambiguities, gaps, and contradictions      │

│ • Classify requirements by priority (MoSCoW)        │

│ • Map requirements to affected system components    │

│ • Determine opt-in governance rules applicability   │

│ • Produce requirements.md artifact for downstream   │

│   consumption                                       │

│ • Validate requirements with user via Q&A rounds    │

│ • Apply overconfidence prevention (question gen)    │

│ • Respect adaptive depth levels based on change     │

│   complexity                                        │

└─────────────────────────────────────────────────────┘

```



---



## 4. Position in the AI-DLC Lifecycle



```mermaid

flowchart LR

    subgraph INCEPTION["Phase 1: INCEPTION"]

        WD[Workspace Detection] --> RA[Requirements Analysis]

        RA --> GT[Governance Triage]

        GT --> US[User Stories]

        US --> WP[Workflow Planning]

        WP --> AD[Application Design]

        AD --> UG[Units Generation]

    end



    subgraph CONSTRUCTION["Phase 2: CONSTRUCTION"]

        FD[Functional Design] --> NFR[NFR Design]

        NFR --> ID[Infrastructure Design]

        ID --> CG[Code Generation]

        CG --> BT[Build & Test]

    end



    subgraph OPERATIONS["Phase 3: OPERATIONS"]

        DV[Delivery Verification]

        ORP[Operational Readiness Pack]

    end



    UG --> FD

    BT --> DV



    style RA fill:#9b59b6,stroke:#333,color:#fff

```



> The **Requirements Analyst** agent is activated at the **Requirements Analysis** stage (highlighted above). It is the first analytical stage — immediately after Workspace Detection establishes the session context.



---



## 5. Invocation & Delegation Flow



```mermaid

sequenceDiagram

    participant User

    participant Orchestrator as aidlc-orchestrator

    participant ReqAnalyst as aidlc-requirements-analyst

    participant Skills as Enterprise Skills/Rules

    participant Steering as Steering Files



    User->>Orchestrator: Start AIDLC / describe intent

    Orchestrator->>Orchestrator: Complete Workspace Detection

    Orchestrator->>Orchestrator: Determine: next stage = Requirements Analysis

    Orchestrator->>Orchestrator: Log: DELEGATED → requirements-analyst

    Orchestrator->>ReqAnalyst: Spawn with session context + user intent



    ReqAnalyst->>Skills: Load aidlc-rules (inception/requirements-analysis.md)

    ReqAnalyst->>Skills: Load aidlc-ent-stn (enterprise standards)

    ReqAnalyst->>Skills: Load aidlc-ent-bp (best practices)

    ReqAnalyst->>Steering: Load workspace steering (tech, structure, product)



    ReqAnalyst->>ReqAnalyst: PHASE 1 — Intent Analysis

    ReqAnalyst->>ReqAnalyst: Analyse user request for scope, domain, constraints

    ReqAnalyst->>ReqAnalyst: PHASE 2 — Gap Detection

    ReqAnalyst->>ReqAnalyst: Identify ambiguities, missing info, contradictions



    ReqAnalyst->>User: Present clarifying questions (multiple-choice format)

    User->>ReqAnalyst: Provide answers



    ReqAnalyst->>ReqAnalyst: PHASE 3 — Requirements Structuring

    ReqAnalyst->>ReqAnalyst: Produce FR list, NFR list, priority classification

    ReqAnalyst->>ReqAnalyst: PHASE 4 — Opt-In Rule Assessment

    ReqAnalyst->>ReqAnalyst: Determine which governance opt-ins apply



    ReqAnalyst->>User: Present requirements.md for validation

    User->>ReqAnalyst: Approve / request changes



    ReqAnalyst->>ReqAnalyst: Finalise requirements artifact

    ReqAnalyst->>Orchestrator: Return validated requirements

    Orchestrator->>Orchestrator: Persist to aidlc-docs/, update state

    Orchestrator->>Orchestrator: Proceed to Governance Triage

```



---



## 6. Multi-Phase Execution Workflow



```mermaid

flowchart TD

    subgraph PHASE1["PHASE 1: INTENT ANALYSIS"]

        I1[Read user's initial request] --> I2[Identify primary objective]

        I2 --> I3[Determine scope: greenfield vs brownfield]

        I3 --> I4[Identify affected systems/components]

        I4 --> I5[Assess change complexity level]

        I5 --> I6[Select adaptive depth level]

    end



    subgraph PHASE2["PHASE 2: GAP DETECTION & QUESTIONING"]

        Q1[Scan for ambiguities in intent] --> Q2[Identify missing information]

        Q2 --> Q3[Check for contradictions]

        Q3 --> Q4[Apply overconfidence prevention]

        Q4 --> Q5[Generate clarifying questions]

        Q5 --> Q6[Format as multiple-choice + Answer format]

        Q6 --> Q7[Present to user in .md file]

        Q7 --> Q8[Process user responses]

        Q8 --> Q9{Sufficient clarity?}

        Q9 -->|No| Q4

        Q9 -->|Yes| R1

    end



    subgraph PHASE3["PHASE 3: REQUIREMENTS STRUCTURING"]

        R1[Extract functional requirements] --> R2[Extract non-functional requirements]

        R2 --> R3[Classify by priority: MoSCoW]

        R3 --> R4[Map to system components]

        R4 --> R5[Identify dependencies & constraints]

        R5 --> R6[Document assumptions]

        R6 --> R7[Write requirements.md]

    end



    subgraph PHASE4["PHASE 4: GOVERNANCE & OPT-IN ASSESSMENT"]

        G1[Evaluate opt-in rules applicability] --> G2[Check: Jira sync needed?]

        G2 --> G3[Check: Security baseline applicable?]

        G3 --> G4[Check: Property-based testing?]

        G4 --> G5[Check: AI compliance needed?]

        G5 --> G6[Check: ADB/ADS required?]

        G6 --> G7[Record opt-in decisions]

        G7 --> G8[Present requirements for validation]

    end



    I6 --> Q1

    R7 --> G1



    style PHASE1 fill:#e8daef,stroke:#6c3483

    style PHASE2 fill:#d4efdf,stroke:#1e8449

    style PHASE3 fill:#d6eaf8,stroke:#2471a3

    style PHASE4 fill:#fdebd0,stroke:#d35400

```



---



## 7. Skills & Steering Files Used



### 7.1 Skills Loaded at Runtime



```mermaid

graph TD

    RA[aidlc-requirements-analyst] --> RULES[aidlc-rules
Stage Rules]

    RA --> ENT_STN[aidlc-ent-stn
Enterprise Standards]

    RA --> ENT_BP[aidlc-ent-bp
Enterprise Best Practices]

    RA --> GUIDE[aidlc-guide
Process Guidance]



    RULES --> REQ_ANALYSIS[inception/requirements-analysis.md
Primary stage rules]

    RULES --> COMMON1[common/question-format-guide.md
Multiple-choice + Answer format]

    RULES --> COMMON2[common/overconfidence-prevention.md
Question generation rules]

    RULES --> COMMON3[common/depth-levels.md
Adaptive depth selection]

    RULES --> COMMON4[common/content-validation.md
Mermaid/ASCII standards]

    RULES --> COMMON5[common/error-handling.md
Error recovery patterns]

    RULES --> COMMON6[common/workflow-changes.md
Mid-workflow change handling]

    RULES --> COMMON7[common/terminology.md
AIDLC glossary]

    RULES --> GOV_TRIAGE[inception/governance-triage.md
Materiality classification prep]



    RULES --> OPTIN1[opt-in/jira-sync/
Jira integration rules]

    RULES --> OPTIN2[opt-in/security-baseline/
Security baseline]

    RULES --> OPTIN3[opt-in/property-based-testing/
PBT rules]

    RULES --> OPTIN4[opt-in/ai-compliance/
AI impact assessment]

    RULES --> OPTIN5[opt-in/adb/
Architecture Design Brief]

    RULES --> OPTIN6[opt-in/ads/
Architecture Design Summary]

    RULES --> OPTIN7[opt-in/standards-check/
ADR standards checking]



    ENT_STN --> STN_IAM[ent-stn-iam.md
IAM requirements identification]

    ENT_STN --> STN_SEC[ent-stn-secrets.md
Secrets handling NFRs]

    ENT_STN --> STN_ENC[ent-stn-encryption.md
Encryption NFRs]

    ENT_STN --> STN_LOG[ent-stn-logging.md
Logging NFRs]

    ENT_STN --> STN_ENV[ent-stn-environments.md
Environment NFRs]

    ENT_STN --> STN_DEV[ent-stn-devops.md
CI/CD NFRs]



    ENT_BP --> BP_API[api-design.md
API quality requirements]

    ENT_BP --> BP_OBS[observability.md
Observability NFRs]

    ENT_BP --> BP_RES[resiliency.md
Resiliency requirements]

    ENT_BP --> BP_SEC[containers.md
Container security NFRs]



    style RA fill:#9b59b6,stroke:#333,color:#fff

    style RULES fill:#e8f5e9,stroke:#363

    style ENT_STN fill:#fde8e8,stroke:#633

    style ENT_BP fill:#dceefb,stroke:#336

    style GUIDE fill:#fff3cd,stroke:#856404

```



### 7.2 Skill Details



| Skill | Type | Purpose | Relevance to Requirements Analyst |

|-------|------|---------|-----------------------------------|

| `aidlc-rules` | Stage Rules | Requirements analysis workflow, question format, depth levels, opt-in assessment | **Primary skill** — governs entire execution flow |

| `aidlc-ent-stn` | Standards (MUST) | Mandatory enterprise requirements for IAM, secrets, encryption, logging, CI/CD | Used to identify implicit NFRs that the user may not have stated |

| `aidlc-ent-bp` | Best Practices (SHOULD) | Recommended patterns for APIs, observability, resiliency, containers | Used to suggest additional NFRs based on the system's nature |

| `aidlc-guide` | Process Guidance | Explains how the AIDLC works, what governance applies, why questions are being asked | Loaded when the user asks process questions during requirements analysis |



### 7.3 Core Stage Rules (from `aidlc-rules`)



| Rule File | What It Controls |

|-----------|-----------------|

| `inception/requirements-analysis.md` | Primary execution logic: phases, question generation, FR/NFR extraction, output format |

| `common/question-format-guide.md` | All questions must be multiple-choice with `[Answer]:` format in .md files |

| `common/overconfidence-prevention.md` | Agent must generate questions even when it thinks it knows the answer — prevents assumptions |

| `common/depth-levels.md` | Adapts analysis depth based on change complexity (trivial → critical) |

| `common/content-validation.md` | Mermaid diagrams, ASCII art standards for requirement docs |

| `common/error-handling.md` | Recovery if questioning rounds fail or produce contradictions |

| `common/workflow-changes.md` | Handles user pivots mid-requirements (scope change, new constraints) |

| `inception/governance-triage.md` | Pre-classification data needed for the next stage (governance triage) |



### 7.4 Opt-In Rules Assessment



During requirements analysis, the agent evaluates which opt-in governance rules should activate for the session:



| Opt-In Rule | Question Asked | Triggers When |

|-------------|---------------|---------------|

| `jira-sync` | Sync requirements/stories to Jira? | Always asked if Jira MCP configured |

| `security-baseline` | Apply security baseline checks? | System handles sensitive data, auth, or external APIs |

| `property-based-testing` | Use property-based testing? | Complex business logic, state machines, data transformations |

| `ai-compliance` | Require AI Impact Assessment? | AI/ML components in scope |

| `adb` | Generate Architecture Design Brief? | Material architecture change (determined by triage) |

| `ads` | Generate Architecture Design Summary? | Minor architecture change (lighter than ADB) |

| `standards-check` | Check ADRs against standards? | **Always on** — runs automatically when ADRs are written |



### 7.5 Steering Files Accessible



The agent has access to all steering files at both levels:



| Scope | Path Pattern | Content |

|-------|-------------|---------|

| Workspace | `.kiro/steering/**/*.md` | Project-specific context (tech stack, structure, product domain) |

| User/Global | `~/.kiro/steering/**/*.md` | Cross-project enterprise rules |



**Current workspace steering files available to the agent:**



| File | How the Agent Uses It |

|------|----------------------|

| `tech.md` | Identifies technical constraints (UFT, VBScript, Windows-only) for requirement feasibility |

| `structure.md` | Maps requirements to existing project modules and isolation boundaries |

| `PRODUCT.md` | Understands domain context (ETC/GTN/JETS trade flow) for domain-specific questions |

| `matching-context.md` | Market matching business rules for matching-related requirements |

| `uft-native-methods.md` | Available UFT capabilities to assess automation feasibility |



---



## 8. Requirements Output Artifact Structure



The requirements analyst produces a structured document at:

```

aidlc-docs/inception/requirements.md

```



### Document Structure



```mermaid

graph TD

    subgraph ARTIFACT["requirements.md"]

        H[Header & Metadata] --> OBJ[Objective Statement]

        OBJ --> SCOPE[Scope Definition]

        SCOPE --> FR[Functional Requirements]

        FR --> NFR[Non-Functional Requirements]

        NFR --> CONST[Constraints & Assumptions]

        CONST --> DEPS[Dependencies]

        DEPS --> RISKS[Risks & Open Questions]

        RISKS --> PRIORITY[Priority Classification]

        PRIORITY --> OPTIN[Opt-In Decisions]

        OPTIN --> TRACE[Traceability Matrix]

    end



    style ARTIFACT fill:#f4ecf7,stroke:#6c3483

```



### Section Details



| Section | Content | Format |

|---------|---------|--------|

| **Objective** | Single paragraph stating what the user wants to achieve | Prose |

| **Scope** | In-scope / out-of-scope boundary | Bullet lists |

| **Functional Requirements** | Numbered FRs with description and acceptance criteria | `FR-001: ...` format |

| **Non-Functional Requirements** | Numbered NFRs categorised by type (performance, security, etc.) | `NFR-001: ...` format |

| **Constraints** | Technical and business limitations | Bullet list |

| **Dependencies** | External systems, teams, or prerequisites | Table |

| **Risks** | Identified risks with likelihood and impact | Table |

| **Priority** | MoSCoW classification (Must/Should/Could/Won't) | Table mapping FRs/NFRs |

| **Opt-In Decisions** | Which governance rules are active for this session | Checklist |

| **Traceability** | Maps each requirement to affected components | Cross-reference table |



---



## 9. Question Generation & Adaptive Depth



### 9.1 Adaptive Depth Levels



```mermaid

flowchart LR

    subgraph DEPTH["Depth Level Selection"]

        D1[Level 1: Trivial
Config change, typo fix] --> Q1["1-2 questions
Confirm only"]

        D2[Level 2: Minor
Bug fix, small feature] --> Q2["3-5 questions
Scope + edge cases"]

        D3[Level 3: Standard
New feature, integration] --> Q3["5-10 questions
Full FR + NFR elicitation"]

        D4[Level 4: Major
New system, migration] --> Q4["10-15 questions
Multi-round deep analysis"]

        D5[Level 5: Critical
Regulated, cross-platform] --> Q5["15+ questions
Exhaustive coverage + governance"]

    end



    style D1 fill:#d4efdf

    style D2 fill:#abebc6

    style D3 fill:#f9e79f

    style D4 fill:#f5b041

    style D5 fill:#e74c3c,color:#fff

```



### 9.2 Question Format (Mandatory)



All questions are written to `.md` files (never in chat) using this format:



```markdown

### Q1: [Question text]



a) Option A  

b) Option B  

c) Option C  

d) Other (please specify)



[Answer]:

```



### 9.3 Overconfidence Prevention



The agent applies systematic doubt even when the user's intent seems clear:



```mermaid

flowchart TD

    INTENT[User states intent] --> OBVIOUS{Seems obvious?}

    OBVIOUS -->|Yes| CHALLENGE[Generate at least 2 challenging questions]

    OBVIOUS -->|No| FULL_QA[Full question generation round]

    CHALLENGE --> VALIDATE[Validate assumptions before proceeding]

    FULL_QA --> VALIDATE

    VALIDATE --> PROCEED[Proceed with confirmed understanding]



    style CHALLENGE fill:#fff3cd,stroke:#856404

```



**Rules:**

- Never assume scope boundaries without confirming

- Never assume NFRs without validating (e.g., don't assume "high availability" — ask)

- Always verify edge cases the user hasn't mentioned

- Always check for unstated constraints (regulatory, performance, cost)



---



## 10. NFR Identification Pattern



```mermaid

flowchart TD

    subgraph NFR_SOURCES["NFR Discovery Sources"]

        S1[User's explicit statements] --> COLLECT[Collect NFRs]

        S2[Enterprise standards scan
aidlc-ent-stn] --> COLLECT

        S3[Best practices scan
aidlc-ent-bp] --> COLLECT

        S4[Domain inference
from steering files] --> COLLECT

        S5[Architecture patterns
implied by scope] --> COLLECT

    end



    COLLECT --> CATEGORISE[Categorise NFRs]



    subgraph CATEGORIES["NFR Categories"]

        C1[Performance & Scalability]

        C2[Security & Compliance]

        C3[Reliability & Availability]

        C4[Observability & Monitoring]

        C5[Maintainability]

        C6[Portability & Deployment]

        C7[Data Integrity & Backup]

        C8[Usability & Accessibility]

    end



    CATEGORISE --> C1

    CATEGORISE --> C2

    CATEGORISE --> C3

    CATEGORISE --> C4

    CATEGORISE --> C5

    CATEGORISE --> C6

    CATEGORISE --> C7

    CATEGORISE --> C8



    style NFR_SOURCES fill:#d6eaf8,stroke:#2471a3

    style CATEGORIES fill:#fdebd0,stroke:#d35400

```



**Enterprise Standards → NFR Mapping:**



| Enterprise Standard | Generated NFR Category | Example NFR |

|--------------------|-----------------------|-------------|

| `ent-stn-iam.md` | Security | "System must authenticate via Okta OIDC with PKCE flow" |

| `ent-stn-secrets.md` | Security | "No secrets stored in source code; use approved vault" |

| `ent-stn-encryption.md` | Security | "All data in transit encrypted with TLS 1.2+" |

| `ent-stn-logging.md` | Observability | "Structured JSON logs with correlation IDs" |

| `ent-stn-environments.md` | Deployment | "Production data never in non-production environments" |

| `ent-stn-devops.md` | Deployment | "All changes via CI/CD pipeline with quality gates" |

| `arch-stn-828-data.md` | Data Integrity | "Data retention per classification tier" |



---



## 11. Critical Rules & Constraints



```mermaid

graph LR

    subgraph HARD_RULES["Hard Rules — Never Violate"]

        R1["All questions in .md files — NEVER in chat"]

        R2["Multiple-choice + Answer format ONLY"]

        R3["Never assume — always verify via questions"]

        R4["Never proceed without user validation"]

        R5["Document ALL assumptions explicitly"]

        R6["Respect adaptive depth levels"]

        R7["Apply overconfidence prevention on every round"]

        R8["Requirements artifact → aidlc-docs/ ONLY"]

    end



    subgraph PROCESS_RULES["Process Rules"]

        P1["Minimum 1 question round even for trivial changes"]

        P2["Maximum 3 question rounds before proceeding"]

        P3["Each round refines — never starts from scratch"]

        P4["Opt-in assessment happens AFTER requirements confirmed"]

        P5["Flag always-on categories even in Explore mode"]

        P6["Log every decision to journal.md"]

    end



    subgraph GOVERNANCE_FLAGS["Always-Flag Categories (even Explore mode)"]

        G1["Vendor dependencies"]

        G2["Regulated data"]

        G3["AI/ML components"]

        G4["Region/jurisdiction changes"]

        G5["Regulated trading concerns"]

    end



    style HARD_RULES fill:#fff3cd,stroke:#856404

    style PROCESS_RULES fill:#d4edda,stroke:#155724

    style GOVERNANCE_FLAGS fill:#fde8e8,stroke:#633

```



---



## 12. Interaction with Other Agents



```mermaid

graph TD

    ORCH[aidlc-orchestrator
Coordinator] -->|delegates| RA[aidlc-requirements-analyst
Requirements Gathering]

    ORCH -->|delegates after RA| PO[aidlc-product-owner
User Stories]

    ORCH -->|delegates after RA| ARCH[aidlc-architect
Application Design]

    ORCH -->|uses RA output in| GT[Governance Triage
Materiality Classification]



    RA -.->|requirements.md feeds| PO

    RA -.->|requirements.md feeds| ARCH

    RA -.->|scope + NFRs feed| GT

    RA -.->|opt-in decisions feed| ORCH



    SEC[aidlc-security-engineer] -.->|consulted if security NFRs complex| RA



    style RA fill:#9b59b6,stroke:#333,color:#fff

    style ORCH fill:#4a90d9,stroke:#333,color:#fff

```



| Downstream Consumer | What It Receives from Requirements Analyst |

|--------------------|-------------------------------------------|

| `aidlc-orchestrator` | Opt-in decisions, scope classification, governance flags |

| **Governance Triage** (orchestrator stage) | Change scope, affected systems, NFR categories → for materiality classification |

| `aidlc-product-owner` | Functional requirements → converts to user stories with acceptance criteria |

| `aidlc-architect` | Full requirements (FR + NFR + constraints) → drives architecture decisions |

| `aidlc-security-engineer` | Security-related NFRs → if on-demand security review is triggered |



| Upstream Input | Source |

|---------------|--------|

| User's initial request | Chat message / prompt |

| Session context (greenfield/brownfield, Explore/Deliver) | `aidlc-orchestrator` via Workspace Detection |

| Existing artefacts (if any) | Previous `aidlc-state.md`, governance artefacts provided by user |

| Workspace steering files | `.kiro/steering/**/*.md` — loaded automatically |



---



## 13. End-to-End Flow: From User Intent to Validated Requirements



```mermaid

sequenceDiagram

    participant U as User

    participant O as Orchestrator

    participant RA as Requirements Analyst

    participant FS as File System



    U->>O: "I want to add swap log filtering to ETC"

    O->>O: Workspace Detection (ETC project, brownfield, VBScript)

    O->>RA: Delegate: Requirements Analysis



    RA->>FS: Read .kiro/steering/PRODUCT.md (domain context)

    RA->>FS: Read .kiro/steering/structure.md (module layout)

    RA->>FS: Read .kiro/steering/tech.md (constraints)



    Note over RA: PHASE 1 — Intent Analysis

    RA->>RA: Scope: ETC SwapLog module (ETCLib_SwapLog.qfl)

    RA->>RA: Complexity: Level 3 (Standard)

    RA->>RA: Depth: 5-10 questions



    Note over RA: PHASE 2 — Questioning

    RA->>FS: Write aidlc-docs/inception/questions-round-1.md

    RA->>U: "Please answer the questions in questions-round-1.md"

    U->>RA: Answers provided



    RA->>RA: Analyse answers, identify remaining gaps

    RA->>FS: Write aidlc-docs/inception/questions-round-2.md

    RA->>U: "A few more clarifications needed"

    U->>RA: Answers provided



    Note over RA: PHASE 3 — Structuring

    RA->>RA: Extract 8 FRs, 5 NFRs

    RA->>RA: Classify: 4 Must, 3 Should, 1 Could



    Note over RA: PHASE 4 — Governance

    RA->>RA: Assess opt-ins: standards-check (always-on), no AI, no vendor

    RA->>FS: Write aidlc-docs/inception/requirements.md



    RA->>U: "Requirements documented — please validate"

    U->>RA: "Approved"



    RA->>FS: Update aidlc-state.md (stage: complete)

    RA->>FS: Update journal.md (logged approval)

    RA->>O: Return: requirements validated, opt-ins recorded

    O->>O: Proceed to Governance Triage

```



---



## 14. Handling Edge Cases



### 14.1 User Provides Existing Requirements



```mermaid

flowchart TD

    START[User provides existing docs
BRD, Jira epic, Confluence page] --> ASSESS[Assess completeness & quality]

    ASSESS --> COMPLETE{Comprehensive?}

    COMPLETE -->|Yes| VALIDATE[Validate with 2-3 confirming questions]

    COMPLETE -->|No| GAP[Identify gaps, generate targeted questions]

    GAP --> MERGE[Merge new answers with existing requirements]

    VALIDATE --> STRUCTURE[Structure into requirements.md format]

    MERGE --> STRUCTURE

    STRUCTURE --> PRESENT[Present for approval]



    style START fill:#d4efdf

```



### 14.2 User Wants to Skip Requirements



```mermaid

flowchart TD

    SKIP[User says: 'skip requirements'] --> RECORD[Record: stage skipped by user request]

    RECORD --> MINIMAL[Generate minimal implicit requirements from intent]

    MINIMAL --> WARN[Flag: downstream gaps may surface later]

    WARN --> PROCEED[Proceed to next stage]



    style SKIP fill:#fde8e8

    style WARN fill:#fff3cd

```



### 14.3 Mid-Requirements Scope Change



```mermaid

flowchart TD

    CHANGE[User changes scope mid-analysis] --> ASSESS[Assess: additive or replacement?]

    ASSESS -->|Additive| EXTEND[Extend existing requirements]

    ASSESS -->|Replacement| RESTART[Restart with new scope
Preserve applicable answers]

    EXTEND --> CONTINUE[Continue questioning]

    RESTART --> CONTINUE



    style CHANGE fill:#fdebd0

```



---



## 15. Governance Preparation (for next stage)



The requirements analyst collects data that feeds directly into Governance Triage:



```mermaid

graph TD

    subgraph TRIAGE_INPUT["Data Prepared for Governance Triage"]

        T1[Change Scope
New/Modify/Replace]

        T2[Affected Systems
List of impacted services]

        T3[Data Classification
Public/Internal/Confidential/Restricted]

        T4[Integration Points
External systems touched]

        T5[Vendor Involvement
Third-party products/services]

        T6[AI/ML Presence
Any AI components?]

        T7[Regional Impact
Jurisdictions affected]

        T8[User Impact
Number of affected users]

    end



    RA[Requirements Analyst] --> T1

    RA --> T2

    RA --> T3

    RA --> T4

    RA --> T5

    RA --> T6

    RA --> T7

    RA --> T8



    T1 --> TRIAGE[Governance Triage Stage]

    T2 --> TRIAGE

    T3 --> TRIAGE

    T4 --> TRIAGE

    T5 --> TRIAGE

    T6 --> TRIAGE

    T7 --> TRIAGE

    T8 --> TRIAGE



    style RA fill:#9b59b6,stroke:#333,color:#fff

    style TRIAGE fill:#f39c12,stroke:#333,color:#fff

```



---



## 16. Completion Criteria



The requirements analyst agent marks its work as **complete** when ALL of these are true:



- [x] User's intent fully understood and documented

- [x] At least one question round completed (overconfidence prevention)

- [x] All functional requirements numbered and described with acceptance criteria

- [x] All non-functional requirements identified (explicit + implied from standards)

- [x] MoSCoW prioritisation applied to all requirements

- [x] Constraints and assumptions documented

- [x] Opt-in governance rules assessed and decisions recorded

- [x] `requirements.md` written to `aidlc-docs/inception/`

- [x] User has explicitly validated the requirements document

- [x] `aidlc-state.md` updated with completion status

- [x] `journal.md` logged with approval timestamp and key decisions



---



## 17. Summary



The `aidlc-requirements-analyst` is a systematic, assumption-challenging, governance-aware requirements elicitation agent that:



1. **Never assumes** — it applies overconfidence prevention to generate questions even when the answer seems obvious

2. **Never asks in chat** — all questions are written to `.md` files in multiple-choice format for traceability

3. **Adapts depth** — trivial changes get 1-2 questions; critical changes get exhaustive multi-round analysis

4. **Identifies implicit NFRs** — scans enterprise standards and best practices to surface requirements the user hasn't stated

5. **Prepares governance data** — collects materiality inputs so the next stage (Governance Triage) can classify the change

6. **Assesses opt-in rules** — determines which governance rules activate for the session based on the change's nature

7. **Produces a structured artifact** — `requirements.md` with FRs, NFRs, priorities, constraints, assumptions, and traceability

8. **Respects session objective** — adjusts rigour based on Explore vs Deliver mode while always flagging the five mandatory categories

9. **Handles pivots gracefully** — mid-analysis scope changes are absorbed, not rejected

10. **Maintains traceability** — every requirement traces back to a user answer, and forward to user stories and architecture decisions



---



*Generated: June 4, 2026 | Source: `~/.kiro/agents/aidlc-requirements-analyst.json` + `aidlc-requirements-analyst.md` + `aidlc-rules` skill*

 