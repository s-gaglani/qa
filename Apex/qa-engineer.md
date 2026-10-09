
# AIDLC QA Engineer Agent — Complete Reference

> **Agent ID:** `qa-engineer`  
> **Full Name:** AI-DLC QA Engineer  
> **Platform:** Kiro (AI-powered Development Environment)  
> **Phase:** Construction → Build and Test  
> **Invocation:** `invoke_sub_agent` with `name: "qa-engineer"`

---

## Overview

The **aidlc-qa-engineer** is a built-in specialist sub-agent within Kiro's AI Development Lifecycle (AIDLC) framework. It is responsible for test strategy, test generation, test execution, quality gates, and build verification. The agent activates during the **Construction phase**, specifically at the **Build and Test** stage — the final stage before the Operations phase begins.

It does not exist as a workspace-level configuration file — it is a system-level agent embedded in Kiro's core, invoked automatically by the **aidlc-orchestrator** or manually via the `invoke_sub_agent` tool.

---

## Agent Position in the AIDLC Lifecycle

```mermaid
graph TD
    subgraph Inception["🔍 INCEPTION PHASE"]
        WD[Workspace Detection]
        RE[Reverse Engineering]
        RA[Requirements Analysis]
        GT[Governance Triage]
        US[User Stories]
        WP[Workflow Planning]
        AD[Application Design]
        UG[Units Generation]
        RFE[Ready for Engineering Gate]
    end

    subgraph Construction["🔨 CONSTRUCTION PHASE (per unit)"]
        FD[Functional Design]
        NFR[NFR Design]
        ID[Infrastructure Design]
        CG[Code Generation]
        BT[🎯 BUILD AND TEST]
    end

    subgraph Operations["🚀 OPERATIONS PHASE"]
        DV[Delivery Verification]
        DAG[Design Approved Gate]
        ORP[Operational Readiness Pack]
    end

    WD --> RE --> RA --> GT --> US --> WP --> AD --> UG --> RFE
    RFE --> FD --> NFR --> ID --> CG --> BT
    BT --> DV --> DAG --> ORP

    style BT fill:#e8f5e9,stroke:#2e7d32,stroke-width:3px
    style Construction fill:#fff3e0,stroke:#ef6c00
    style Inception fill:#e3f2fd,stroke:#1565c0
    style Operations fill:#fce4ec,stroke:#c62828
```

> The QA Engineer agent is activated at the **Build and Test** node (highlighted in green).

---

## Responsibilities & Capabilities

```mermaid
mindmap
  root((QA Engineer Agent))
    Test Strategy
      Risk-based test selection
      Coverage analysis
      Test type classification
      Test priority assignment
    Test Generation
      Unit test creation
      Integration test scaffolding
      End-to-end test scripts
      Property-based testing (opt-in)
    Test Execution
      Build verification
      Test runner orchestration
      Result collection
      Failure triage
    Quality Gates
      Pass/fail criteria enforcement
      Coverage thresholds
      Regression detection
      Gate progression control
    Evidence & Reporting
      Test evidence collection
      Root Cause Analysis (RCA)
      Allure-style report generation
      Governance artefact production
```

---

## Invocation Workflow

```mermaid
sequenceDiagram
    participant User as User / Orchestrator
    participant QA as qa-engineer Agent
    participant Code as Codebase
    participant Report as Reports

    User->>QA: Invoke (after Code Generation complete)
    QA->>Code: Read generated code & test infrastructure
    QA->>QA: Generate test strategy
    QA->>Code: Write test files
    QA->>QA: Execute tests (build + run)

    alt Tests Pass
        QA->>Report: Generate test evidence
        QA->>User: ✅ Quality gate PASSED
    else Tests Fail
        QA->>QA: Root Cause Analysis
        QA->>User: ❌ Quality gate FAILED + RCA report
        User->>QA: Fix and re-run
    end
```

---

## Skills & Steering Files Used

The QA Engineer agent leverages the following internal references and skill files when activated:

### Core References (Always Loaded)

| Reference File | Purpose |
|---------------|---------|
| `references/construction/build-and-test.md` | Primary rules for the Build & Test stage — defines what the agent must do, quality gate criteria, and output format |
| `references/common/process-overview.md` | AIDLC workflow overview for stage awareness |
| `references/common/content-validation.md` | Ensures generated content (Mermaid, ASCII) is valid |
| `references/common/error-handling.md` | Error recovery patterns for test failures |

### Opt-In Rules (Conditional)

| Opt-In Rule | Activation Trigger | What It Does |
|------------|-------------------|--------------|
| `references/opt-in/test-evidence/` | Governance requires evidence | Collects structured test evidence + generates RCA for failures |
| `references/opt-in/property-based-testing/` | User opts in during requirements | Enables generative/property-based testing strategies |
| `references/opt-in/security-baseline/` | Security-sensitive change | Adds security-focused test assertions |

### Steering Files (Workspace-Level Context)

When operating in a specific workspace, the agent also consumes workspace steering:

```mermaid
graph LR
    subgraph Steering["📋 Workspace Steering (always loaded)"]
        TECH[tech.md
Stack & commands]
        STRUCT[structure.md
Project layout]
        PROD[PRODUCT.md
Domain context]
    end

    subgraph Conditional["📋 Conditional Steering"]
        MATCH[matching-context.md
ETC matching scenarios]
        UFT[uft-native-methods.md
UFT methods reference]
    end

    QA((qa-engineer))
    Steering --> QA
    Conditional -.->|if matching files read| QA
```

---

## Quality Gate Decision Flow

```mermaid
flowchart TD
    Start([Build & Test Stage Begins]) --> Build[Run Build/Compile]
    Build --> BuildOK{Build Success?}
    BuildOK -->|No| BuildFix[Report build errors
Return to Code Gen]
    BuildOK -->|Yes| RunTests[Execute Test Suite]

    RunTests --> Results{All Tests Pass?}
    Results -->|Yes| Coverage[Check Coverage Thresholds]
    Results -->|No| RCA[Root Cause Analysis]

    RCA --> Severity{Failure Severity?}
    Severity -->|Critical| Block[❌ GATE BLOCKED
Must fix before proceeding]
    Severity -->|Non-Critical| Advisory[⚠️ Advisory
Log & proceed with acknowledgment]

    Coverage --> CovOK{Meets Threshold?}
    CovOK -->|Yes| Evidence[Generate Test Evidence]
    CovOK -->|No| CovReport[Report coverage gaps
Suggest additional tests]

    Evidence --> Gate[✅ QUALITY GATE PASSED]
    Gate --> Next([→ Operations Phase])

    Block --> Fix[Fix Issues]
    Fix --> RunTests

    style Gate fill:#c8e6c9,stroke:#2e7d32
    style Block fill:#ffcdd2,stroke:#c62828
    style Advisory fill:#fff9c4,stroke:#f9a825
```

---

## Integration with Other Agents

```mermaid
graph TB
    subgraph Upstream["Agents That Feed INTO QA Engineer"]
        ARCH[architect
Design specs, NFRs]
        BACK[backend-developer
Generated backend code]
        FRONT[frontend-developer
Generated frontend code]
        DB[database-engineer
Schema migrations]
        DEVOPS[devops-engineer
IaC, CI/CD pipelines]
    end

    QA((qa-engineer))

    subgraph Downstream["Agents That RECEIVE FROM QA Engineer"]
        ORCH[aidlc-orchestrator
Gate pass/fail signal]
        SEC[security-engineer
Security test findings]
        SRE[sre-engineer
Performance baselines]
        TW[technical-writer
Test documentation]
    end

    ARCH -->|design contracts| QA
    BACK -->|source code| QA
    FRONT -->|source code| QA
    DB -->|migrations| QA
    DEVOPS -->|test infrastructure| QA

    QA -->|gate result| ORCH
    QA -->|security findings| SEC
    QA -->|perf metrics| SRE
    QA -->|test docs| TW

    style QA fill:#e8f5e9,stroke:#2e7d32,stroke-width:3px
```

---

## Input / Output Contract

### Inputs (What the QA Agent Receives)

| Input | Source | Description |
|-------|--------|-------------|
| Generated source code | Code Generation stage | The code to be tested |
| Design specifications | Functional Design stage | What the code should do |
| NFR requirements | NFR Design stage | Performance, security, accessibility targets |
| Infrastructure config | Infrastructure Design stage | Test environment setup |
| User stories + acceptance criteria | Inception phase | Business validation criteria |
| Existing test infrastructure | Workspace | Current test framework, patterns, conventions |

### Outputs (What the QA Agent Produces)

| Output | Consumer | Description |
|--------|----------|-------------|
| Test files | Workspace | Generated test code matching project conventions |
| Test execution results | Orchestrator | Pass/fail with counts and details |
| Quality gate decision | Orchestrator | PASS / FAIL / ADVISORY |
| Test evidence artefact | ORP (Operations) | Structured evidence for governance |
| RCA report | Developer agents | Root cause analysis for failures |
| Coverage report | Orchestrator | Code and requirement coverage metrics |

---

## Governance Integration

```mermaid
flowchart LR
    subgraph GovernanceChecks["Governance Checks Performed"]
        TE[Test Evidence
opt-in/test-evidence/]
        PBT[Property-Based Testing
opt-in/property-based-testing/]
        SB[Security Baseline
opt-in/security-baseline/]
    end

    subgraph GovernanceOutputs["Feeds Into"]
        DV[Delivery Verification]
        ORP[Operational Readiness Pack]
        PTO[Permit to Operate]
    end

    QA((qa-engineer))
    GovernanceChecks --> QA
    QA --> GovernanceOutputs
```

When **test-evidence** opt-in is active, the QA Engineer produces:
- Structured JSON test results
- Traceability matrix (test ↔ requirement)
- Root Cause Analysis for any failures
- Evidence summary for the ORP

---

## Session Modes & Behaviour

| Session Objective | QA Agent Behaviour |
|-------------------|-------------------|
| **Explore** | Lighter testing — smoke tests, basic assertions, no coverage gates |
| **Deliver** | Full testing — strategy, generation, execution, gates enforced, evidence produced |

| Governance Mode | QA Agent Behaviour |
|----------------|-------------------|
| **Active** | Quality gate findings presented immediately; blocks progression if Critical |
| **Shadow** | Findings logged to `governance-shadow-log.md`; no blocking |

---

## Tools Available to the QA Agent

The qa-engineer agent has access to the full Kiro toolset:

| Tool Category | Usage |
|---------------|-------|
| **File Read/Write** | Read source code, write test files |
| **Shell Execution** | Run build commands, test runners, linters |
| **Search** | Find test patterns, existing test infrastructure |
| **Web Search** | Look up testing best practices, library docs |
| **Diagnostics** | Check for compile/lint errors in generated tests |
| **Sub-Agent Invocation** | Can delegate to general-task-execution for parallel test runs |

---

## Example Invocation

```
invoke_sub_agent(
  name: "qa-engineer",
  prompt: "Generate and execute tests for the new ETCLib_Matching module. 
           Follow the existing VBScript/UFT patterns in TestSuites/ETC/. 
           Use the Allure reporting pattern (InitReport → ReportTestStepEvent → GenerateAllureReport).
           Validate against the scenarios in ScenarioDocumentation/GTN_ETC Matching Features 2.xlsx.",
  explanation: "Delegating test generation and execution for the matching feature to the QA specialist agent.",
  contextFiles: [
    { path: "d:\\uft_automation\\Libraries\\ETC\\ETCLib_Matching.qfl" },
    { path: "d:\\uft_automation\\Libraries\\Shared\\ReportLibrary.qfl" },
    { path: "d:\\uft_automation\\ScenarioDocumentation\\GTN_ETC Matching Features  2.xlsx" }
  ]
)
```

---

## Summary

The `aidlc-qa-engineer` is a **system-level specialist agent** that:

1. **Activates** during the Construction phase's Build and Test stage
2. **Consumes** code from developer agents + design from architect
3. **Generates** test strategy, test code, and executes tests
4. **Enforces** quality gates before allowing progression to Operations
5. **Produces** test evidence for governance (ORP, PtO, CAB)
6. **Integrates** with workspace steering files for project-specific conventions
7. **Supports** both Explore (light) and Deliver (full) testing modes

It is the final checkpoint between building code and shipping it.

---

*Generated: 2026-06-03 | Agent Version: Built-in (Kiro Platform)* 