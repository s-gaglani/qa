# AIDLC Database Engineer Agent — High-Fidelity Reference



> **Generated:** June 9, 2026  

> **Source:** AI-DLC skills, workflow guides, and enterprise standards



---



## Table of Contents



1. [Agent Overview](#1-agent-overview)

2. [Role in the AI-DLC Lifecycle](#2-role-in-the-ai-dlc-lifecycle)

3. [Activation & Trigger Points](#3-activation--trigger-points)

4. [Internal Workflow](#4-internal-workflow)

5. [Skills & Knowledge Sources](#5-skills--knowledge-sources)

6. [Enterprise Standards Applied](#6-enterprise-standards-applied)

7. [Relationships with Other Agents](#7-relationships-with-other-agents)

8. [Inputs & Outputs](#8-inputs--outputs)

9. [Decision Logic](#9-decision-logic)

10. [End-to-End Sequence Diagram](#10-end-to-end-sequence-diagram)

11. [Key Constraints & Guardrails](#11-key-constraints--guardrails)



---



## 1. Agent Overview



The **`database-engineer`** is a specialist sub-agent in the AI-DLC (AI-assisted Development Lifecycle) framework. It is responsible for the **data layer** of any software system being built — covering schema design, data modelling, migrations, query optimisation, and database technology selection.



| Property | Value |

|---|---|

| **Agent Name** | `database-engineer` |

| **Phase** | AI-DLC Construction |

| **Stage** | Code Generation (data layer) |

| **Primary Language** | Context-dependent (SQL, HCL, VBScript stubs, etc.) |

| **Invoked By** | `aidlc-orchestrator` or directly by user |

| **Returns To** | Orchestrator / calling context |



### Core Capabilities



- **Schema Design** — tables, indexes, constraints, relationships

- **Data Modelling** — entity-relationship modelling, normalisation, DDD aggregate mapping

- **Migrations** — forwards and backwards migration scripts

- **Query Optimisation** — explain plans, indexing strategies, N+1 detection

- **Database Selection** — recommends the right store (RDBMS, NoSQL, DynamoDB, etc.) per use case

- **Enterprise Compliance** — enforces TP ICAP STN-828 data architecture and backup standards



---



## 2. Role in the AI-DLC Lifecycle



The AI-DLC has three top-level phases. The `database-engineer` operates exclusively in **Construction**, specifically during **Code Generation**.



```mermaid

flowchart LR

    subgraph INCEPTION["🔍 INCEPTION Phase"]

        A1[Workspace Detection]

        A2[Requirements Analysis]

        A3[Governance Triage]

        A4[User Stories]

        A5[Application Design]

        A6[Units Generation]

    end



    subgraph CONSTRUCTION["🔨 CONSTRUCTION Phase (per unit)"]

        B1[Functional Design]

        B2[NFR Design]

        B3[Infrastructure Design]

        B4[Code Generation]:::dbstage

        B5[Build & Test]

    end



    subgraph OPERATIONS["🚀 OPERATIONS Phase"]

        C1[Delivery Verification]

        C2[Design Approved Gate]

        C3[Operational Readiness Pack]

    end



    INCEPTION --> CONSTRUCTION

    CONSTRUCTION --> OPERATIONS

    B4 --> |"database-engineer\nactivated here"| B4



    classDef dbstage fill:#f4a261,stroke:#e76f51,color:#000

```



Within **Code Generation**, the orchestrator decomposes work by concern and routes the data-layer work to `database-engineer` while simultaneously routing frontend, backend, and infrastructure work to their respective agents.



---



## 3. Activation & Trigger Points



The agent is invoked when any of the following conditions are true during Construction:



```mermaid

flowchart TD

    ORCH[aidlc-orchestrator] --> CHECK{Does this unit\nof work involve\na data layer?}

    CHECK -->|Yes| ROUTE{What data\nconcern?}

    CHECK -->|No| SKIP[Skip — no DB work\nin this unit]



    ROUTE --> R1[Schema Design\nor Migration]

    ROUTE --> R2[Query / Index\nOptimisation]

    ROUTE --> R3[Database Technology\nSelection]

    ROUTE --> R4[DynamoDB\nSingle-Table Design]

    ROUTE --> R5[Data Architecture\nCompliance Check]



    R1 --> DB_ENG[database-engineer\nAgent]:::active

    R2 --> DB_ENG

    R3 --> DB_ENG

    R4 --> DB_ENG

    R5 --> DB_ENG



    classDef active fill:#2a9d8f,color:#fff,stroke:#264653

```



### Direct User Invocation



The user can also invoke the agent directly from Kiro chat by asking:



- *"Design the database schema for…"*

- *"Optimise this query…"*

- *"Which database should I use for…"*

- *"Help me write a migration for…"*



---



## 4. Internal Workflow



Once invoked, the agent follows a structured internal process:



```mermaid

flowchart TD

    START([Agent Receives Task]) --> CONTEXT[Read Context\nRequirements, design docs,\nexisting schema if brownfield]

    CONTEXT --> ASSESS{Greenfield or\nBrownfield?}



    ASSESS -->|Greenfield| TECH_SEL[Technology Selection\nRDBMS vs NoSQL vs DynamoDB\nbased on access patterns]

    ASSESS -->|Brownfield| ANALYSE[Analyse Existing Schema\nIdentify gaps, drift,\nmigration needs]



    TECH_SEL --> DDD[Map Domain Model → Data Model\nAggregates → Tables/Collections\nValue Objects → Embedded docs\nDomain Events → Event stores]

    ANALYSE --> DDD



    DDD --> SCHEMA[Generate Schema\nDDL / Terraform / CDK\nIndexes + Constraints]



    SCHEMA --> STD_CHECK{STN-828\nCompliance?}

    STD_CHECK -->|Fail| REMEDIATE[Adjust Schema\nAdd encryption at rest\nTag resources, fix retention]

    STD_CHECK -->|Pass| MIGRATIONS



    REMEDIATE --> MIGRATIONS[Generate Migration Scripts\nUp + Down scripts\nIdempotent guards]



    MIGRATIONS --> QUERIES[Query Layer\nORM models, DAL stubs,\nor raw SQL optimised]

    QUERIES --> BACKUP[Define Backup Strategy\nRPO/RTO targets per\ncriticality tier]

    BACKUP --> REVIEW[Self-Review\nExplain plans, N+1 checks,\nSecurity posture]

    REVIEW --> OUTPUT([Return Artefacts\nto Orchestrator])

```



### Step-by-Step Breakdown



| Step | What Happens |

|---|---|

| **1. Read Context** | Agent consumes requirements, application design, DDD model, NFR targets, and any existing schema |

| **2. Greenfield / Brownfield Split** | For new systems: tech selection + full schema. For existing: gap analysis + targeted migrations |

| **3. Technology Selection** | Evaluates RDBMS (PostgreSQL, Aurora), NoSQL (MongoDB, DocumentDB), and DynamoDB against access patterns, scale, and enterprise standards |

| **4. DDD → Data Model Mapping** | Aggregates become consistency boundaries (tables or DynamoDB top-level items). Value Objects become embedded columns/attributes. Domain Events become event-store entries |

| **5. Schema Generation** | Produces DDL, Terraform HCL, or AWS CDK depending on target. Includes indexes, constraints, encryption config |

| **6. STN-828 Compliance Check** | Verifies store selection aligns with data architecture standard, encryption at rest is on, retention is defined, tagging is present |

| **7. Migration Scripts** | Forwards + backwards, idempotent, safe for zero-downtime deploys |

| **8. Query Layer** | ORM model stubs, DAL interfaces, or raw SQL as appropriate |

| **9. Backup Strategy** | RPO/RTO defined, three-layer DynamoDB backup or equivalent RDBMS strategy per criticality |

| **10. Self-Review** | Checks explain plans, spots N+1 risks, verifies security posture before handing off |



---



## 5. Skills & Knowledge Sources



The agent draws on several AI-DLC skills and reference documents:



```mermaid

mindmap

  root((database-engineer))

    aidlc-ddd-modeling

      Aggregate design

      Entity vs Value Object

      Domain Events

      Repository pattern

      Bounded contexts

    aidlc-ent-bp

      dynamodb.md

        Single-table design

        10 DynamoDB principles

        Key hierarchy

        GSI patterns

      backup.md

        3-layer backup

        RPO/RTO tiers

      observability.md

        Query metrics

        Slow query logging

    aidlc-ent-stn

      arch-stn-828-data.md

        Store selection rules

        Config management

        Data retention

      ent-stn-encryption.md

        AES-256 at rest

        Key management

      ent-stn-logging.md

        Audit trail requirements

    aidlc-rules

      construction/functional-design.md

      construction/nfr-requirements.md

      construction/code-generation.md

```



### Key Reference Documents



| Reference | What It Provides | Type |

|---|---|---|

| `arch-stn-828-data.md` | Mandatory store selection criteria, config management, retention policy | **Standard (MUST)** |

| `ent-stn-encryption.md` | AES-256 at rest, key management, CSPRNG | **Standard (MUST)** |

| `dynamodb.md` | Single-table design, 10 principles, GSI patterns | **Best Practice (SHOULD)** |

| `backup.md` | RPO/RTO tiers, three-layer DynamoDB backup | **Best Practice (SHOULD)** |

| `observability.md` | Query metrics, structured logging | **Best Practice (SHOULD)** |

| `aidlc-ddd-modeling` skill | Aggregate → table mapping, bounded context data isolation | **Design Pattern** |



---



## 6. Enterprise Standards Applied



The agent is a key enforcer of TP ICAP's data-layer governance:



```mermaid

flowchart LR

    subgraph MUST["🔴 MUST (Mandatory — STN standards)"]

        M1["STN-828: Use approved data stores\n(no unapproved tech)"]

        M2["Encryption at rest: AES-256"]

        M3["Data retention policy defined\nper data classification"]

        M4["Resource tagging:\nenv, owner, cost-centre"]

        M5["Config management separate\nfrom application data"]

    end



    subgraph SHOULD["🟡 SHOULD (Best Practices)"]

        S1["DynamoDB: Single-table design\nwhere appropriate"]

        S2["Three-layer backup:\nPITR + on-demand + export"]

        S3["RPO ≤ 1h for critical,\n≤ 24h for standard"]

        S4["Observability: slow query\nalerts + query metrics"]

        S5["Idempotent migrations\n(safe to re-run)"]

    end



    DB_ENG[database-engineer] --> MUST

    DB_ENG --> SHOULD

```



### Compliance Failure Handling



If the agent detects a violation of a `MUST` standard:



1. It **does not proceed** with schema generation

2. It **flags the violation** with the relevant STN reference

3. It **proposes a compliant alternative**

4. It **logs to** `governance-shadow-log.md` (or presents in active mode)



---



## 7. Relationships with Other Agents



The `database-engineer` does not work in isolation. It has defined interfaces with six other agents:



```mermaid

flowchart TB

    ORCH[🎯 aidlc-orchestrator\nCoordinates all agents\nroutes tasks]



    ARCH[🏛️ architect\nProvides DDD domain model\nADRs with DB technology choices\nbounded context map]



    BACKEND[⚙️ backend-developer\nConsumes DB schema\nGenerates repository layer\nORM models, DAL]



    DEVOPS[🔧 devops-engineer\nConsumes DB infrastructure spec\nGenerates Terraform / CDK\nfor RDS, DynamoDB, Aurora]



    SEC[🔐 security-engineer\nReviews encryption config\nAudit trail completeness\nIAM roles for DB access]



    QA[🧪 qa-engineer\nConsumes schema for\ntest data setup scripts\nDB integration tests]



    SRE[📡 sre-engineer\nReceives backup strategy\nSlow query alert config\nRPO/RTO targets]



    DB_ENG[🗄️ database-engineer]:::center



    ORCH -->|"delegates DB work"| DB_ENG

    DB_ENG -->|"returns schema\n+ migrations"| ORCH



    ARCH -->|"domain model\nDB tech ADRs"| DB_ENG

    DB_ENG -->|"schema decisions\nfeed back into\narchitecture docs"| ARCH



    DB_ENG -->|"schema DDL\nORM models\nDAL interfaces"| BACKEND

    DB_ENG -->|"DB infrastructure spec\nIaC requirements"| DEVOPS

    DB_ENG -->|"encryption config\naudit requirements"| SEC

    DB_ENG -->|"test data scripts\nschema fixtures"| QA

    DB_ENG -->|"backup strategy\nobservability config"| SRE



    classDef center fill:#e9c46a,stroke:#f4a261,color:#000,font-weight:bold

```



### Interaction Detail



| Agent | What DB Engineer Receives | What DB Engineer Provides |

|---|---|---|

| **aidlc-orchestrator** | Task assignment, unit of work definition | Completed schema, migrations, compliance sign-off |

| **architect** | DDD domain model, bounded contexts, DB technology ADRs | Schema decisions, trade-offs, any ADR amendments |

| **backend-developer** | Consumes the schema | DDL, ORM model stubs, repository interface definitions |

| **devops-engineer** | Consumes infrastructure spec | Terraform/CDK resources (RDS, DynamoDB, Aurora clusters) |

| **security-engineer** | Reviews the agent's output | Encryption configuration, IAM role requirements for DB |

| **qa-engineer** | Uses schema for test setup | Test data seed scripts, schema fixtures |

| **sre-engineer** | Operationalises the backup plan | Backup strategy document, slow query alert thresholds |



---



## 8. Inputs & Outputs



### Inputs



```mermaid

flowchart LR

    I1["📋 Requirements\n(functional + NFRs)"] --> DB_ENG

    I2["🏛️ Domain Model\n(DDD aggregates,\nbounded contexts)"] --> DB_ENG

    I3["📐 Application Design\n(ADRs, tech choices,\narchitecture diagrams)"] --> DB_ENG

    I4["🗂️ Existing Schema\n(brownfield only —\ncurrent DDL, ERD)"] --> DB_ENG

    I5["⚙️ NFR Targets\n(RPO, RTO, latency,\nscale targets)"] --> DB_ENG

    I6["🏷️ Enterprise Standards\n(STN-828, encryption,\nlogging standards)"] --> DB_ENG



    DB_ENG[🗄️ database-engineer]

```



### Outputs



```mermaid

flowchart LR

    DB_ENG[🗄️ database-engineer]



    DB_ENG --> O1["📄 Schema DDL\nor Terraform HCL\n(tables, indexes,\nconstraints)"]

    DB_ENG --> O2["🔄 Migration Scripts\n(up + down,\nidempotent)"]

    DB_ENG --> O3["🐍 ORM / DAL Stubs\n(repository interfaces,\nmodel classes)"]

    DB_ENG --> O4["💾 Backup Strategy\n(RPO/RTO, PITR config,\nexport schedule)"]

    DB_ENG --> O5["📊 Query Guidance\n(indexes, explain plans,\nanti-pattern flags)"]

    DB_ENG --> O6["✅ Compliance Sign-off\n(STN-828 checklist\nitems resolved)"]

```



---



## 9. Decision Logic



### Technology Selection Decision Tree



```mermaid

flowchart TD

    START([New Data Store Needed]) --> Q1{Known access\npatterns up front?}



    Q1 -->|No| RDBMS[Recommend RDBMS\nPostgreSQL / Aurora\nFlexible querying]

    Q1 -->|Yes| Q2{Primarily key-value\nor hierarchical lookups?}



    Q2 -->|No| Q3{Complex joins\nor reporting needed?}

    Q2 -->|Yes| Q4{Scale to millions\nof items or TPS > 1k?}



    Q3 -->|Yes| RDBMS

    Q3 -->|No| DYNAMO_SMALL[DynamoDB\nLow scale, simple access]



    Q4 -->|Yes| DYNAMO_SCALE[DynamoDB\nSingle-table design]

    Q4 -->|No| DYNAMO_SMALL



    RDBMS --> STN_CHECK{STN-828\napproved store?}

    DYNAMO_SCALE --> STN_CHECK

    DYNAMO_SMALL --> STN_CHECK



    STN_CHECK -->|Yes| PROCEED[Proceed with\nSchema Generation]

    STN_CHECK -->|No| BLOCK[🚫 Block\nFlag to orchestrator\nPropose approved alternative]

```



### Migration Strategy Decision Tree



```mermaid

flowchart TD

    MIG_START([Migration Required]) --> RISK{Breaking\nchange?}



    RISK -->|No| ADDITIVE[Additive migration\nadd columns/tables\nno downtime]

    RISK -->|Yes| ZERO_DOWN{Zero-downtime\nrequired?}



    ZERO_DOWN -->|No| SIMPLE[Simple migration\nwith maintenance window\ndocument downtime]

    ZERO_DOWN -->|Yes| EXPAND[Expand-Contract\npattern\nPhase 1: Add new\nPhase 2: Migrate data\nPhase 3: Remove old]



    ADDITIVE --> IDEM[Add idempotency\nguard: IF NOT EXISTS]

    SIMPLE --> IDEM

    EXPAND --> IDEM



    IDEM --> ROLLBACK[Write rollback\n'down' script]

    ROLLBACK --> TEST_GUIDE[Flag to qa-engineer:\nneeds migration\nintegration test]

```



---



## 10. End-to-End Sequence Diagram



This shows the full journey of a typical database engineering task within a Construction cycle:



```mermaid

sequenceDiagram

    actor User

    participant ORCH as aidlc-orchestrator

    participant ARCH as architect

    participant DB as database-engineer

    participant BACKEND as backend-developer

    participant DEVOPS as devops-engineer

    participant SEC as security-engineer

    participant QA as qa-engineer



    User->>ORCH: "Build the trade data layer\nfor JETS market 231"

    ORCH->>ARCH: Request domain model\nand DB technology ADRs

    ARCH-->>ORCH: DDD model: Trade aggregate,\nInstrument VO, MarketEvent\nADR: Aurora PostgreSQL chosen



    ORCH->>DB: Delegate: Design schema\nfor Trade, Instrument,\nMarketEvent (Aurora PostgreSQL)

   

    Note over DB: Reads domain model,\nNFRs, STN-828 standard



    DB->>DB: Map aggregates → tables

    DB->>DB: Generate DDL + indexes

    DB->>DB: STN-828 compliance check

    DB->>DB: Write migrations (up/down)

    DB->>DB: Define backup: PITR + daily export



    DB-->>ORCH: Schema DDL, migrations,\nDAL interfaces, backup spec



    ORCH->>BACKEND: Here is the schema\nGenerate repository layer

    ORCH->>DEVOPS: Here is the DB spec\nGenerate Aurora Terraform

    ORCH->>SEC: Review encryption config\nand IAM for DB access



    BACKEND-->>ORCH: Repository classes, ORM models

    DEVOPS-->>ORCH: Terraform for Aurora cluster,\nparameter groups, KMS key

    SEC-->>ORCH: ✅ Encryption at rest OK\n✅ IAM least-privilege OK



    ORCH->>QA: Schema + seed scripts ready\nGenerate integration tests

    QA-->>ORCH: Migration tests,\nquery integration tests



    ORCH-->>User: ✅ Data layer complete\nSchema, migrations, DAL,\nTerraform, tests all generated

```



---



## 11. Key Constraints & Guardrails



### Hard Rules (Agent Will Not Violate)



| Constraint | Reason |

|---|---|

| Will not generate schema for unapproved data stores | STN-828 mandatory requirement |

| Will not omit encryption at rest | Enterprise encryption standard |

| Will not skip backup strategy definition | Enterprise backup standard |

| Will not generate migrations without rollback scripts | Data safety |

| Will not produce schema without resource tagging | AWS standards, cost governance |



### Soft Rules (Flagged, Not Blocked)



| Guideline | What Happens if Skipped |

|---|---|

| Single-table DynamoDB design | Agent flags and explains trade-offs, documents in ADR |

| Idempotent migration guards | Agent flags the risk, asks for explicit acknowledgement |

| Slow query observability hooks | Flagged to SRE agent for implementation |

| Test data seed scripts | Flagged to QA agent |



### Governance Integration



```mermaid

flowchart LR

    DB_ENG[database-engineer\nproduces output]



    DB_ENG --> |"STN-828\nviolation found"| GOV_ACTIVE{Governance\nMode?}

    GOV_ACTIVE -->|Active| PRESENT[Present finding\nto user immediately\nBlock until resolved]

    GOV_ACTIVE -->|Shadow| LOG[Log to\ngovernance-shadow-log.md\nContinue silently]



    DB_ENG --> |"SHOULD violation\n(best practice miss)"| WARN[Add note to\noutput artefact\nDo not block]

```



### Interaction with Opt-In Rules



The following AI-DLC opt-in governance flows can affect the database-engineer's output:



| Opt-In Rule | How It Affects DB Engineer |

|---|---|

| **standards-check** (always-on) | Every DB technology ADR written is checked against STN-828 and approved store list |

| **security-review** | Triggers `security-engineer` to audit DB encryption, IAM, and audit logging after schema is complete |

| **test-evidence** | `qa-engineer` must produce and pass migration integration tests before the unit is marked done |

| **delivery-verification** | In Operations phase, the generated schema is compared against application design — drift is flagged |



---



## Summary



The `database-engineer` agent is the **data layer specialist** in the AI-DLC Construction phase. It translates domain models from the architect into compliant, production-ready schemas, migrations, and backup strategies. It is invoked by the orchestrator, receives design context from the architect, and feeds its outputs to the backend developer, DevOps engineer, security engineer, QA engineer, and SRE.



Its outputs are governed by two mandatory TP ICAP standards (STN-828 data architecture and enterprise encryption) and several best-practice references (DynamoDB single-table design, backup tiers, observability patterns). It will not produce schema that violates mandatory standards and integrates with the governance pipeline for both active and shadow modes.



---



*Document generated by Kiro from AI-DLC skill disclosures and workflow analysis.*

 