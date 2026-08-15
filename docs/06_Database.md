\# AEGIS Database Design



| Version | Date | Author | Status |

|---|---|---|---|

| 1.0 | 15 August 2026 | Arya Solanki | Draft |



\---



\# 1. Database Objective



The AEGIS database will securely store the structured information required by the platform.



The database must support:



\- Users

\- Profiles

\- Conversations

\- Messages

\- Memories

\- Skills

\- Permissions

\- Devices

\- Health information

\- Workflow information

\- Tasks

\- Audit logs

\- System settings



The database architecture must prioritize security, data ownership, scalability, and maintainability.



\---



\# 2. Primary Database



AEGIS will initially use:



\*\*PostgreSQL\*\*



PostgreSQL will store structured application data.



Semantic/vector data will initially use:



\*\*pgvector\*\*



This allows relational and vector data to be managed within the same database system during the early stages of development.



\---



\# 3. Database Architecture



```text

&#x20;                   PostgreSQL

&#x20;                       |

&#x20;       ┌───────────────┼────────────────┐

&#x20;       |               |                |

&#x20;       ▼               ▼                ▼

&#x20;   Identity         Application       AI Data

&#x20;       |               |                |

&#x20;       ▼               ▼                ▼

&#x20;   Users          Conversations      Memories

&#x20;   Profiles       Messages           Embeddings

&#x20;   Permissions    Tasks              Documents

```



\---



\# 4. Main Entities



The initial database will contain the following major entities:



```text

users

profiles

conversations

messages

memories

memory\_embeddings

skills

skill\_permissions

user\_skills

devices

device\_permissions

tasks

workflow\_events

health\_records

audit\_logs

system\_settings

```



\---



\# 5. Users Table



The `users` table represents authenticated AEGIS users.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Unique user identifier |

| username | VARCHAR | User's username |

| email | VARCHAR | User email |

| password\_hash | TEXT | Secure password hash |

| role | VARCHAR | User role |

| status | VARCHAR | Account status |

| created\_at | TIMESTAMP | Account creation |

| updated\_at | TIMESTAMP | Last update |



Passwords must never be stored in plaintext.



\---



\# 6. Profiles Table



The `profiles` table contains user-specific profile information.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Profile identifier |

| user\_id | UUID | Related user |

| display\_name | VARCHAR | Display name |

| language | VARCHAR | Preferred language |

| timezone | VARCHAR | User timezone |

| preferences | JSONB | General preferences |

| created\_at | TIMESTAMP | Creation time |

| updated\_at | TIMESTAMP | Update time |



\---



\# 7. Conversations Table



Stores conversation sessions.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Conversation ID |

| user\_id | UUID | Owner |

| title | VARCHAR | Conversation title |

| created\_at | TIMESTAMP | Creation time |

| updated\_at | TIMESTAMP | Last activity |

| archived | BOOLEAN | Archive status |



\---



\# 8. Messages Table



Stores individual conversation messages.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Message ID |

| conversation\_id | UUID | Related conversation |

| role | VARCHAR | User/assistant/system/tool |

| content | TEXT | Message content |

| provider | VARCHAR | AI provider |

| model | VARCHAR | AI model |

| token\_count | INTEGER | Token usage |

| created\_at | TIMESTAMP | Creation time |



\---



\# 9. Memories Table



Stores long-term memories.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Memory ID |

| user\_id | UUID | Owner |

| category | VARCHAR | Memory category |

| content | TEXT | Memory content |

| importance | FLOAT | Importance score |

| source | VARCHAR | Memory source |

| created\_at | TIMESTAMP | Creation time |

| updated\_at | TIMESTAMP | Update time |

| expires\_at | TIMESTAMP | Optional expiration |

| deleted\_at | TIMESTAMP | Soft deletion |



\---



\# 10. Memory Categories



Initial categories include:



```text

personal

preference

project

learning

workflow

device

conversation

health

goal

other

```



Health-related memories must have separate privacy controls.



\---



\# 11. Memory Embeddings



The `memory\_embeddings` structure will store vector representations used for semantic search.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Embedding ID |

| memory\_id | UUID | Related memory |

| embedding | VECTOR | Vector representation |

| model | VARCHAR | Embedding model |

| created\_at | TIMESTAMP | Creation time |



pgvector will be used for similarity search.



\---



\# 12. Skills Table



Stores installed Skills.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Skill ID |

| name | VARCHAR | Skill name |

| version | VARCHAR | Skill version |

| description | TEXT | Skill description |

| source | VARCHAR | Skill source |

| status | VARCHAR | Installation status |

| created\_at | TIMESTAMP | Installation time |

| updated\_at | TIMESTAMP | Update time |



\---



\# 13. Skill Permissions



Defines permissions required by each Skill.



\### Examples



```text

filesystem.read

filesystem.write

microphone

camera

network

browser

notifications

contacts

calendar

health

device\_control

```



\---



\# 14. User Skills



Stores which Skills a user has enabled.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Record ID |

| user\_id | UUID | User |

| skill\_id | UUID | Skill |

| enabled | BOOLEAN | Enabled status |

| configuration | JSONB | User configuration |

| created\_at | TIMESTAMP | Creation time |



\---



\# 15. Devices Table



Stores authorized devices.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Device ID |

| user\_id | UUID | Owner |

| name | VARCHAR | Device name |

| type | VARCHAR | Device type |

| manufacturer | VARCHAR | Manufacturer |

| model | VARCHAR | Model |

| network\_identifier | TEXT | Device identifier |

| status | VARCHAR | Current status |

| paired\_at | TIMESTAMP | Pairing time |

| last\_seen\_at | TIMESTAMP | Last known activity |



AEGIS must never assume that a discovered network device is authorized.



\---



\# 16. Device Permissions



Stores permissions granted to AEGIS for a device.



Examples:



```text

read\_status

control

notifications

media

power

configuration

```



\---



\# 17. Tasks Table



Stores scheduled or asynchronous tasks.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Task ID |

| user\_id | UUID | Owner |

| type | VARCHAR | Task type |

| description | TEXT | Task description |

| schedule | JSONB | Schedule information |

| status | VARCHAR | Task status |

| next\_run\_at | TIMESTAMP | Next execution |

| last\_run\_at | TIMESTAMP | Previous execution |

| created\_at | TIMESTAMP | Creation time |



\---



\# 18. Workflow Events



Stores permitted activity information used for workflow analysis.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Event ID |

| user\_id | UUID | User |

| application | VARCHAR | Application |

| event\_type | VARCHAR | Event type |

| metadata | JSONB | Additional information |

| started\_at | TIMESTAMP | Start time |

| ended\_at | TIMESTAMP | End time |



Workflow collection must be explicitly enabled by the user.



\---



\# 19. Health Records



Health information requires additional privacy controls.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Record ID |

| user\_id | UUID | User |

| source | VARCHAR | Data source |

| record\_type | VARCHAR | Sleep/activity/etc. |

| value | JSONB | Health data |

| recorded\_at | TIMESTAMP | Measurement time |

| created\_at | TIMESTAMP | Database time |



Health data must not be exposed to Skills unless the user grants appropriate permission.



\---



\# 20. Audit Logs



Audit logs record important system actions.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Log ID |

| user\_id | UUID | User |

| action | VARCHAR | Action performed |

| module | VARCHAR | Responsible module |

| resource\_type | VARCHAR | Resource |

| resource\_id | UUID | Resource ID |

| result | VARCHAR | Success/failure |

| metadata | JSONB | Additional information |

| created\_at | TIMESTAMP | Event time |



Examples:



```text

skill.installed

skill.removed

permission.granted

permission.revoked

device.paired

automation.executed

memory.created

memory.deleted

settings.changed

```



\---



\# 21. System Settings



Stores application-level settings.



\### Fields



| Field | Type | Description |

|---|---|---|

| id | UUID | Setting ID |

| user\_id | UUID | User |

| key | VARCHAR | Setting name |

| value | JSONB | Setting value |

| updated\_at | TIMESTAMP | Update time |



Sensitive secrets must not be stored directly in this table.



\---



\# 22. Relationships



The major relationships are:



```text

User

&#x20;|

&#x20;+---- Profile

&#x20;|

&#x20;+---- Conversations

&#x20;|          |

&#x20;|          +---- Messages

&#x20;|

&#x20;+---- Memories

&#x20;|          |

&#x20;|          +---- Embeddings

&#x20;|

&#x20;+---- Skills

&#x20;|

&#x20;+---- Devices

&#x20;|

&#x20;+---- Tasks

&#x20;|

&#x20;+---- Workflow Events

&#x20;|

&#x20;+---- Health Records

&#x20;|

&#x20;+---- Audit Logs

&#x20;|

&#x20;+---- Settings

```



\---



\# 23. Data Ownership



Every user-owned record shall be associated with the appropriate user identifier.



AEGIS must enforce user-level data isolation.



One user must never be able to access another user's:



\- Conversations

\- Memories

\- Health data

\- Devices

\- Tasks

\- Workflow data

\- Settings



\---



\# 24. Data Deletion



Users shall be able to delete their data.



Deletion categories may include:



```text

Conversation Data

Memory Data

Workflow Data

Health Data

Device Data

Account Data

```



Deletion operations must be carefully designed to prevent accidental loss of unrelated data.



\---



\# 25. Data Export



Future versions shall support exporting user data.



Potential formats:



```text

JSON

CSV

Markdown

```



Exported data should be machine-readable and portable.



\---



\# 26. Database Security



The database shall use:



\- Strong authentication

\- Least-privilege database users

\- Encrypted connections

\- Secure credentials

\- Access controls

\- Regular backups

\- Audit logging



Database credentials must never be committed to Git.



\---



\# 27. Indexing Strategy



Indexes should be created for frequently queried fields.



Potential indexes:



```text

users.email

conversations.user\_id

messages.conversation\_id

memories.user\_id

memories.category

memory\_embeddings.embedding

skills.name

devices.user\_id

tasks.user\_id

workflow\_events.user\_id

health\_records.user\_id

audit\_logs.user\_id

audit\_logs.created\_at

```



Indexes should be added based on actual query patterns and measured performance.



\---



\# 28. Backup Strategy



The production database should support:



\- Scheduled backups

\- Backup verification

\- Recovery testing

\- Retention policies

\- Encrypted backup storage



Backup strategy will be finalized before production deployment.



\---



\# 29. Migration Strategy



Database schema changes shall use version-controlled migrations.



The migration system should allow:



```text

Create migration

&#x20;      ↓

Test migration

&#x20;      ↓

Apply migration

&#x20;      ↓

Verify database

&#x20;      ↓

Commit migration

```



Migrations must not be manually edited in production without a documented recovery plan.



\---



\# 30. Development Database



Development should use an isolated database environment.



Recommended development setup:



```text

Docker

&#x20;  |

&#x20;  +-- PostgreSQL

&#x20;  |

&#x20;  +-- pgvector

```



Production credentials must never be reused in development.



\---



\# 31. Database Technology Summary



```text

Primary Database

&#x20;   PostgreSQL



Vector Extension

&#x20;   pgvector



Optional Cache

&#x20;   Redis



Database Migrations

&#x20;   Version-controlled migration system



Development

&#x20;   Docker PostgreSQL

```



\---



\# 32. Database Design Principles



The database shall follow:



\- Data isolation

\- Least privilege

\- Referential integrity

\- Secure defaults

\- Minimal data collection

\- Clear ownership

\- Auditable operations

\- Recoverability

\- Scalability



\---



\# 33. Future Database Expansion



Future versions may introduce additional entities for:



\- Mobile devices

\- Wearables

\- Organizations

\- Teams

\- Skill marketplace

\- Multi-agent sessions

\- Robotics

\- Vehicles

\- Advanced analytics

\- Enterprise administration



These should be added only when required by the corresponding system features.



\---



\# End of Database Design

