<div align="center">

# Azure Management Groups, Subscriptions, Microsoft Entra ID & RBAC

### An architecture-first guide to organizing Azure resources and controlling access at scale

![Microsoft Azure](https://img.shields.io/badge/Microsoft_Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Microsoft Entra ID](https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=for-the-badge)
![Azure RBAC](https://img.shields.io/badge/Azure-RBAC-5E5E5E?style=for-the-badge)
![Cloud Governance](https://img.shields.io/badge/Cloud-Governance-107C10?style=for-the-badge)
![Cloud Architecture](https://img.shields.io/badge/Cloud-Architecture-243A5E?style=for-the-badge)
![DevOps](https://img.shields.io/badge/DevOps-Learning_Journey-D83B01?style=for-the-badge)
![Documentation](https://img.shields.io/badge/Documentation-Technical_Notes-444444?style=for-the-badge)
![Markdown](https://img.shields.io/badge/Markdown-000000?style=for-the-badge&logo=markdown&logoColor=white)

**[Diagrams](#20-original-architecture-diagrams)** · **[Enterprise Scenario](#19-real-world-enterprise-scenario)** · **[Medium Articles](#22-my-related-medium-articles)** · **[Microsoft Docs](#23-official-microsoft-documentation)**

</div>

---

## Table of Contents

1. [Project Introduction](#1-project-introduction)
2. [Project Overview](#2-project-overview)
3. [Learning Objectives](#3-learning-objectives)
4. [Azure Architecture Overview](#4-azure-architecture-overview)
5. [Azure Resource Hierarchy](#5-azure-resource-hierarchy)
6. [Azure Management Groups](#6-azure-management-groups)
7. [Azure Subscriptions](#7-azure-subscriptions)
8. [Azure Resource Groups](#8-azure-resource-groups)
9. [Microsoft Entra ID](#9-microsoft-entra-id)
10. [Authentication vs Authorization](#10-authentication-vs-authorization)
11. [Azure RBAC](#11-azure-rbac)
12. [Azure Built-in Roles](#12-azure-built-in-roles)
13. [Azure RBAC vs Microsoft Entra ID Roles](#13-azure-rbac-vs-microsoft-entra-id-roles)
14. [Azure RBAC Scope](#14-azure-rbac-scope)
15. [User Onboarding and Offboarding](#15-user-onboarding-and-offboarding)
16. [Group-Based RBAC](#16-group-based-rbac)
17. [Least Privilege Principle](#17-least-privilege-principle)
18. [Enterprise Security Best Practices](#18-enterprise-security-best-practices)
19. [Real-World Enterprise Scenario](#19-real-world-enterprise-scenario)
20. [Original Architecture Diagrams](#20-original-architecture-diagrams)
21. [Key Takeaways](#21-key-takeaways)
22. [My Related Medium Articles](#22-my-related-medium-articles)
23. [Official Microsoft Documentation](#23-official-microsoft-documentation)
24. [About the Author](#24-about-the-author)

---

## 1. Project Introduction

Every Azure environment, from a single-subscription sandbox to a multi-division enterprise, has to answer two questions:

1. **How are resources organized?** Tenant Root Group → Management Groups → Subscriptions → Resource Groups → Resources.
2. **Who can do what, and where?** Microsoft Entra ID identities → Azure RBAC roles → scopes.

Getting these wrong is expensive. Without a clear hierarchy, governance is applied inconsistently and costs are hard to attribute. Without disciplined access control, people end up with more permissions than they need, which is one of the most common causes of accidental outages and security exposure.

This repository documents my understanding of both questions through **five original architecture diagrams** and written technical explanations. Where a diagram simplifies how Azure actually behaves, the text beside it explains the technically correct behavior.

> [!NOTE]
> **Learning and documentation project.** This repository explains concepts and architecture patterns. It does **not** contain deployed infrastructure, Infrastructure as Code, or production configuration, and it is not a production or professional enterprise implementation.
>
> The diagrams are **original educational illustrations**. Names such as *KGF*, *Rangoli*, *Dhondhu*, *Bhakua* and *Tinku*, and the amount "18000 Rs.", are **fictional teaching examples**. Annotations are written in Hinglish (Hindi in English letters); an English translation is in [`docs/additional-notes.md`](docs/additional-notes.md).

---

## 2. Project Overview

| Item | Details |
|---|---|
| **Topic** | Management Groups, Subscriptions, Resource Groups, Microsoft Entra ID, Azure RBAC, Cloud Governance |
| **Format** | Five original diagrams, each with a written technical explanation |
| **Scope** | Resource hierarchy, identity, authentication vs authorization, role assignment, group-based access, least privilege |
| **Audience** | Azure learners, Cloud and DevOps engineers, anyone reviewing my learning |
| **Status** | Conceptual learning documentation. No deployed resources |
| **Companion content** | [Medium articles](#22-my-related-medium-articles) |

### Repository structure

```text
azure-management-and-rbac/
│
├── README.md
│
├── images/
│   ├── 01-management-group-hierarchy.png
│   ├── 02-azure-account-and-entra-id.png
│   ├── 03-user-access-and-rbac.png
│   ├── 04-subscription-and-role-management.png
│   └── 05-entra-id-groups-and-security.png
│
└── docs/
    └── additional-notes.md
```

---

## 3. Learning Objectives

- Explain how a **tenant**, **Tenant Root Group**, **management groups**, **subscriptions**, **resource groups** and **resources** fit together.
- Distinguish a **Microsoft Entra ID tenant** from an **Azure subscription**.
- Distinguish **authentication** (who you are) from **authorization** (what you may do).
- Distinguish **Azure RBAC roles** (Owner, Contributor, Reader) from **Microsoft Entra roles** (Global Administrator, User Administrator, Privileged Role Administrator).
- Build a **role assignment** from three parts: *who* + *which role* + *which scope*.
- Apply **group-based RBAC** and **least privilege** instead of assigning roles to individuals.
- Describe how users are **onboarded and offboarded** across identity, access and organization layers.

---

## 4. Azure Architecture Overview

Azure separates **identity** from **resource organization**. Azure RBAC connects the two.

| Plane | Component | Responsibility |
|---|---|---|
| **Identity** | Microsoft Entra ID tenant | Users, groups, authentication, directory roles |
| **Organization** | Tenant Root Group → Management Groups → Subscriptions → Resource Groups | Governance, billing boundaries, resource lifecycle |
| **Access** | Azure RBAC | Connects an identity to a role at a scope |

```text
        ┌──────────────────────────────────────┐
        │  Microsoft Entra ID tenant           │  ← Identity: who you are
        │  users · groups · Entra roles        │
        └──────────────────┬───────────────────┘
                           │ trusted by
                           ▼
        ┌──────────────────────────────────────┐
        │  Tenant Root Group                   │  ← Organization: where things live
        │  └─ Management Groups                │
        │     └─ Subscriptions                 │
        │        └─ Resource Groups            │
        │           └─ Resources               │
        └──────────────────┬───────────────────┘
                           │
              Azure RBAC role assignment            ← Access: what you can do, and where
```

---

## 5. Azure Resource Hierarchy

| Level | What it is | Typical purpose |
|---|---|---|
| **Tenant** | The Microsoft Entra ID directory for an organization | Identity boundary: users, groups, applications |
| **Tenant Root Group** | Top-level management group of the tenant | Governance and access that must reach every subscription |
| **Management Group** | Container for subscriptions and child management groups | Group subscriptions by business unit, environment or compliance need |
| **Subscription** | Billing, access and limits boundary | Isolate teams, workloads or environments |
| **Resource Group** | Logical container for resources that share a lifecycle | Deploy, manage and delete related resources together |
| **Resource** | A single service instance (VM, storage account, and so on) | The actual workload components |

**Inheritance:** access and governance applied at a higher level flow **downward** to everything beneath it.

---

## 6. Azure Management Groups

A **management group** is a governance scope **above subscriptions**. It lets you apply governance (Azure Policy) and access control (Azure RBAC) to many subscriptions at once.

| Concept | Explanation |
|---|---|
| **Tenant Root Group** | The single top-level management group in a directory. Its ID is the same as the tenant ID, and it cannot be moved or deleted |
| **Parent management group** | A management group that has others (or subscriptions) beneath it |
| **Child management group** | A management group nested under a parent |
| **Multiple management groups** | A parent can have many children, for example one per business unit |
| **Policy inheritance** | A policy assigned to a management group applies to all nested management groups, subscriptions and resources |
| **Access inheritance** | An Azure role assigned to a management group is inherited by everything beneath it |
| **Enterprise governance** | One assignment at a management group replaces repeating the same assignment on every subscription |

**Rules worth remembering**

- Each management group and each subscription has exactly **one** parent; a management group can have **many** children.
- A hierarchy supports up to **six levels** of depth, not counting the root level or the subscription level.
- A management group contains subscriptions and other management groups, **not resources directly**.
- All subscriptions in a management group must trust the **same** Microsoft Entra tenant.
- New subscriptions land under the Tenant Root Group by default and can then be moved.
- No one has access to the root management group by default; a Global Administrator can **elevate access** to gain it.

> [!NOTE]
> **Clarification of Diagram 1.** The diagram says a management group is the "top level container". More precisely, the **Tenant Root Group** is the top-level management group and every other management group sits beneath it. For **cost**, management groups give organizational structure; spend itself is tracked per subscription and resource group, usually with tags and Microsoft Cost Management.

### Management Group vs Resource Group

| Aspect | Management Group | Resource Group |
|---|---|---|
| Contains | Subscriptions and child management groups | Azure resources |
| Position | **Above** subscriptions | **Below** subscriptions |
| Purpose | Governance and access at scale | Lifecycle of related resources |
| Holds resources directly | No | Yes |
| Example | `HR MG`, `Sales MG` | `rg-dev`, `rg-prod` |

---

## 7. Azure Subscriptions

A **subscription** is the unit of **billing** and an **access and limits boundary**.

- A subscription trusts **exactly one** Microsoft Entra tenant; one tenant can have **many** subscriptions.
- Costs are reported per subscription, which makes subscriptions a natural boundary for teams, workloads or environments.
- Service quotas and limits apply per subscription.
- Moving a subscription between management groups changes which inherited policies and role assignments apply to it.

### Tenant vs Subscription

| Aspect | Microsoft Entra ID Tenant | Azure Subscription |
|---|---|---|
| Role | Identity directory (users, groups, apps) | Billing and resource container |
| Identifier | Tenant ID | Subscription ID |
| Contains | Identities and directory objects | Resource groups and resources |
| Governed by | Entra roles (directory tasks) | Azure RBAC (resource tasks) |
| Relationship | One tenant → many subscriptions | Each subscription → one trusted tenant |

> [!NOTE]
> **"Azure Account" is a teaching label.** Diagram 2 uses "Azure Account - KGF" as one box. In Microsoft's terms you sign in as an identity in an Entra tenant, and the billing and resource boundary is the subscription. The three items in Diagram 2 (a tenant, a first user, and the Tenant Root Group) are a good mental model. Technically, the root management group is created when management groups are first used in the tenant, so treat it as always present conceptually.

---

## 8. Azure Resource Groups

- Every resource belongs to **exactly one** resource group.
- Resources in a group usually share a **lifecycle**: deployed, updated and deleted together.
- A resource group lives **inside one subscription**.
- RBAC at resource group scope gives narrow access, for example only to the development environment.

My diagrams use resource groups to separate environments: `rg-dev`, `rg-qa`, `rg-uat`, `rg-prod`, plus a workload group, `babli-rg`.

---

## 9. Microsoft Entra ID

**Microsoft Entra ID** (formerly Azure Active Directory) is Microsoft's cloud identity and access management service and the **identity provider** for Azure.

| Concept | Explanation |
|---|---|
| **Tenant** | A dedicated instance of Entra ID for an organization, identified by a Tenant ID |
| **Users** | Identities for people, stored in the directory |
| **Groups** | Collections of users (and other objects) used to manage access together |
| **Authentication** | Entra ID verifies credentials (password, OTP/MFA) and issues a token |
| **Identity management** | Creating, updating, disabling and deleting identities across their lifecycle |
| **Administrative roles** | Entra roles such as Global Administrator and User Administrator that manage the directory |

The diagrams show a tenant on the **Free** license, which covers basic user and group management.

---

## 10. Authentication vs Authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question | Who are you? | What are you allowed to do? |
| Handled by | Microsoft Entra ID | Azure RBAC (for Azure resources) |
| Inputs | Username, password, OTP/MFA | Role + scope |
| Result | A **token** proving identity | Actions allowed or denied |
| Diagram metaphor | The **Gate**: "Andar ghusna = Authentication" (*entering = authentication*) | What you may do once inside |

**Practical example**

1. A user opens `portal.azure.com` and signs in with username, password and OTP. Entra ID verifies them and issues a token. **This is authentication.**
2. The user opens a resource group. Azure checks their role assignments at that scope. **This is authorization.**
3. If the user has no role assignment, they sign in successfully but see no resources.

> [!IMPORTANT]
> A user can sign in successfully and still see **nothing** in Azure. Authentication succeeded, but with no role assignment there is no authorization to any resource.

---

## 11. Azure RBAC

**Azure role-based access control (Azure RBAC)** is the authorization system for Azure resources, built on Azure Resource Manager. Diagram 3 summarizes it: *to give any user access you need two things, **Scope** and **Role**.* In full, a role assignment has three elements:

| Element | Question | Example |
|---|---|---|
| **Security principal** | Who? | A user, group, service principal or managed identity |
| **Role definition** | What can they do? | Owner, Contributor, Reader |
| **Scope** | Where does it apply? | Management group, subscription, resource group, resource |

**Why it is required.** Not everyone who can sign in should be able to delete a VM or change a network rule. RBAC lets you grant exactly the access a person or workload needs, at exactly the level it is needed.

**Additive model.** A principal's effective permissions are the **sum** of all their role assignments across scopes.

**Inheritance.** Role assignments at a scope apply to every child scope beneath it.

---

## 12. Azure Built-in Roles

| Role | Permissions | Can assign Azure RBAC roles? | Typical use |
|---|---|---|---|
| **Owner** | Full management access to all resources, **including assigning Azure RBAC roles** | **Yes** | A very small set of trusted administrators |
| **Contributor** | Create and manage all resource types, but **cannot assign Azure RBAC roles** | **No** | Engineers and DevOps teams |
| **Reader** | View resources without modifying them | No | Auditors, stakeholders |

**Differences, precisely**

- **Owner vs Contributor:** the key difference is access delegation. Owner can grant access to others; Contributor cannot.
- **Contributor vs Reader:** Contributor can create, change and delete resources; Reader can only view.
- Only roles with role-assignment permissions, such as **Owner** or **User Access Administrator**, can create role assignments.

> [!TIP]
> Azure has many more built-in roles than these three, including narrower service-specific ones (for example *Virtual Machine Contributor*). Owner, Contributor and Reader are the foundational three used throughout my diagrams.

---

## 13. Azure RBAC vs Microsoft Entra ID Roles

These are **two separate role systems**. Both appear at the top of Diagrams 2 and 3.

| Aspect | Azure RBAC roles | Microsoft Entra roles |
|---|---|---|
| **Manage what** | Azure **resources** (VMs, networks, storage) | The **directory**: users, groups, app registrations, tenant settings |
| **Examples** | Owner, Contributor, Reader | Global Administrator, User Administrator, Privileged Role Administrator |
| **Scope** | Management group, subscription, resource group, resource | Tenant (or an administrative unit) |
| **Managed in** | Access control (IAM) blade | Microsoft Entra admin center → Roles and administrators |
| **Authorization question** | "What can you do with Azure resources?" | "What can you do in the directory?" |

### Roles discussed in this project

| Role | System | Purpose |
|---|---|---|
| **Global Administrator** | Entra | Highest directory privilege; can manage almost all aspects of Entra ID |
| **User Administrator** | Entra | Creates and manages users and groups, the role relevant to onboarding |
| **Privileged Role Administrator** | Entra | Manages role assignments in Entra ID and Privileged Identity Management |
| **Owner** | Azure RBAC | Full resource access plus the ability to assign Azure roles |
| **Contributor** | Azure RBAC | Manage resources, no role assignment |
| **Reader** | Azure RBAC | View resources only |

### Global Administrator vs Subscription Owner

| | Global Administrator | Subscription Owner |
|---|---|---|
| System | Microsoft Entra roles | Azure RBAC |
| Controls | The directory | Everything in one subscription, including granting access |
| Automatic access to the other? | **No** | **No** |

> [!WARNING]
> A Global Administrator does **not** automatically have access to Azure subscriptions. They can **elevate access** to manage all subscriptions and management groups in the tenant, but that is a deliberate, auditable action. Conversely, an Azure Owner cannot create Entra users unless they also hold a suitable Entra role.

---

## 14. Azure RBAC Scope

Azure RBAC has four scope levels, and assignments **inherit downward**:

```text
Management Group     ← broadest
   └─ Subscription
        └─ Resource Group
             └─ Resource     ← narrowest
```

| Scope | Effect of a Contributor assignment here |
|---|---|
| Management group | All subscriptions (and everything inside them) beneath it |
| Subscription | All resource groups and resources in it |
| Resource group | Only resources in that group |
| Individual resource | Only that single resource |

### Assignments shown in my diagrams

| Diagram | Assignment |
|---|---|
| 3 | **Tinku**: Owner at **HR MG**, Contributor at **Sales MG** |
| 4 | **Bhakua**: Owner on **dhondhu-subscription** |
| 4 | **DevOps Contributors** group: Contributor on the subscription |
| 4 | **rg-dev-contributors** group: Contributor on `rg-dev` |
| 5 | **Bhakua Engineer (Global Admin)**: Owner on the subscription |

> [!TIP]
> Choose the **narrowest scope** that lets someone do their job. A developer who only works in `rg-dev` should be assigned at `rg-dev`, not the subscription.

---

## 15. User Onboarding and Offboarding

Diagram 4 notes that subscription creation, management group creation and the association between them together make up "onboarding and offboarding". Applied to a person, a secure lifecycle looks like this:

| Stage | Identity (Entra ID) | Access (Azure RBAC) | Organization |
|---|---|---|---|
| **Onboarding** | Create the user (needs a role such as User Administrator) | Add the user to the right **group**; the group already holds the role at the right scope | The team's subscription already sits under the right management group |
| **Role change** | Move the user between groups | Access changes through group membership | No change |
| **Offboarding** | Remove from groups; disable or delete the account | Access ends with group membership | Retire or move the subscription if the whole team is dissolved |

> [!NOTE]
> Diagram 3's note ("so that Tinku can onboard users") reflects that onboarding is an **Entra directory task**. It needs an Entra role such as **User Administrator**; an Azure Owner assignment alone does not provide it.

---

## 16. Group-Based RBAC

Diagram 4 shows the three-step pattern:

```text
1. Group creation            → create a security group in Microsoft Entra ID
2. User addition in group    → add people who need the same access
3. Role assignment on group  → assign the Azure role to the group at the right scope
```

| Direct user assignments | Group-based assignments |
|---|---|
| One assignment per person per scope | One assignment per group per scope |
| Hard to audit as teams grow | Easy to review: "who is in this group?" |
| Offboarding means hunting down every assignment | Offboarding means removing group membership |
| Role sprawl and mistakes | Consistent, repeatable model |

A **security group** is an Entra ID group used to grant access to resources. A role assigned to the group applies to every member.

### Groups shown in Diagrams 4 and 5

| Group | Members shown | Role and scope |
|---|---|---|
| **DevOps Contributors / Subscription Group** (D4) | Mr. Bill, Mr. Gill, Mr. Till | Contributor on `dhondhu-subscription` |
| **rg-dev-contributors** (D4) | Not shown | Contributor on `rg-dev` |
| **Azure Executive Control Board** (D5) | Billu (Architect), Tillu (Manager), Lillu (Architect2) | Role not labeled in the diagram |
| **Subscription Contributor** (D5) | Dhillu (Lead), Killu (DevOps) | Contributor at subscription scope (implied by name) |
| **Subscription Reader** (D5) | Not shown | Reader at subscription scope (implied by name) |
| **RG DEV CONTRIBUTORS** (D5) | Pillu, Rillu (Interns) | Contributor on `rg-dev` (implied by name) |
| **SECURITY GROUP** (D5) | Inspector | Role not labeled in the diagram |

> [!NOTE]
> Where a diagram does not label a role or membership, this table says so rather than guessing.

---

## 17. Least Privilege Principle

**Least privilege** means giving each identity only the permissions it needs, at the narrowest scope, for only as long as needed. It limits the damage from mistakes and compromised accounts.

Diagram 5 states three rules at the top, which together express it:

| Rule in the diagram | Professional interpretation |
|---|---|
| "Minimum privileged role dena hai" | Grant only the permissions required for the task |
| "Kisi ko Owner nahi dena hai, Only Contributor" | Keep Owner for a very small trusted set; give engineers Contributor |
| "Group based RBAC use karna hai" | Assign roles to groups, not individuals |

Interns in Diagram 5 receive Contributor only on `rg-dev`: smallest role, narrowest scope, via a group.

---

## 18. Enterprise Security Best Practices

These are widely recommended practices beyond what the diagrams show. Verify details against the [Microsoft documentation](#23-official-microsoft-documentation); some capabilities need specific licenses.

| Practice | What it means |
|---|---|
| **Least privilege** | Smallest role at the narrowest scope that works |
| **Group-based access** | Assign roles to groups, never to scattered individuals |
| **Regular access reviews** | Periodically remove stale assignments and group members (Microsoft Entra access reviews; licensing applies) |
| **Privileged access management** | Prefer just-in-time elevation with Microsoft Entra Privileged Identity Management over permanent Owner or Global Administrator (licensing applies) |
| **Role assignment auditing** | Review who changed role and policy assignments through the Azure Activity Log |
| **Separation of duties** | Do not let one person both request and approve sensitive changes; keep identity administration and resource administration with different people where practical |
| **Secure identity lifecycle** | Remove access promptly at role change or exit; require MFA, especially for administrators |
| **Governance policies** | Use Azure Policy at management group level for guardrails such as allowed regions and required tags |

Additional notes:

- Keep **Global Administrator** assignments few, and use separate accounts for daily and privileged work.
- Keep **more than one** trusted owner or administrator so access never depends on one person. Diagram 5 shows a single Owner to illustrate "very few owners".
- Give `prod` the tightest access and keep `dev`, `qa`, `uat` and `prod` separate.

---

## 19. Real-World Enterprise Scenario

> [!NOTE]
> This is a **conceptual example** for learning. It is **not** an actual deployment. It extends the structure in Diagram 1 with an IT department.

```text
Tenant Root Group
│
├── HR Management Group
│   ├── Recruitment Management Group
│   │   └── Recruitment Subscription
│   │       └── Resource Groups
│   └── HR Subscription
│       └── Resource Groups
│
├── Sales Management Group
│   ├── Sales Subscription
│   │   └── Resource Groups
│   └── Ads Subscription
│       └── Resource Groups
│
└── IT Management Group
    ├── IT Shared Services Subscription
    │   └── rg-dev · rg-qa · rg-uat · rg-prod
    └── IT Security Subscription
        └── Resource Groups
```

### How the pieces work together

| Component | Role in the design |
|---|---|
| **Management groups** | Separate HR, Sales and IT so each gets its own governance and access rules |
| **Subscriptions** | Billing and isolation boundary per team or workload |
| **Resource groups** | Group resources by workload or environment |
| **Microsoft Entra ID** | Holds employee identities and authenticates them |
| **Security groups** | Collect users by function, for example "Sales Subscription Contributors" |
| **Azure RBAC role assignments** | Bind each group to a role at a scope |
| **Least privilege** | Keeps each role and scope as small as the job allows |

### Example access design

| Who (Entra group) | Role | Scope | Why |
|---|---|---|---|
| Sales cloud engineers | Contributor | Sales Subscription | Build and manage workloads, cannot grant access |
| Ads engineers | Contributor | An Ads resource group | Narrower, limited to one workload |
| IT interns | Contributor | `rg-dev` in IT Shared Services | Development only, never production |
| Auditors | Reader | HR Management Group | View-only across all HR subscriptions |
| Platform administrators (very few) | Owner | Subscription or management group | Manage access and governance |
| Identity administrators | *Entra role:* User Administrator | Tenant | Onboard and manage users and groups |

### End-to-end flow

1. A new Sales engineer joins; an identity administrator **creates the user** in Entra ID.
2. The user is **added to the Sales engineers group**.
3. That group already holds **Contributor on the Sales Subscription**, so no per-user assignment is needed.
4. The user signs in (**authentication**), receives a token, and Azure evaluates RBAC (**authorization**).
5. When the engineer leaves, removing them from the group and disabling the account ends their access.

---

## 20. Original Architecture Diagrams

> [!NOTE]
> These are my original educational diagrams. Names are fictional, and Hinglish annotations are translated in [`docs/additional-notes.md`](docs/additional-notes.md).

### Diagram 1: Azure Management Group Hierarchy

![Azure Management Group Hierarchy](images/01-management-group-hierarchy.png)

**Introduction.** How Azure resources are organized beneath the Tenant Root Group.

**Technical explanation.** Two child management groups, **HR** and **Sales**, hang from the root. HR contains a child management group (**Recruitment HR Team**, with a Recruitment subscription) and a subscription (**Rangoli**). Sales contains two subscriptions (**Sales** and **Ads**). Each subscription holds a resource group containing virtual machines. A user (**Dhondhu**) is connected to the Ads resource group.

**Important concepts.** Tenant Root Group; parent and child management groups; subscriptions grouped under management groups; subscription → resource group → resource nesting; downward inheritance.

**Enterprise use case.** A compliance team applies HR-specific policy once at the HR management group and it covers every subscription beneath it, while Sales stays under a separate governance umbrella.

**Key learning points.**
- Management groups organize subscriptions; resource groups organize resources.
- Governance and access flow from parent to child.
- The hierarchy supports security, cost organization and compliance.

> [!TIP]
> **Accuracy note.** The Tenant Root Group, not a generic management group, is the true top level. See [Azure Management Groups](#6-azure-management-groups).

---

### Diagram 2: Azure Account, Microsoft Entra ID and the Authentication Flow

![Azure Account and Microsoft Entra ID](images/02-azure-account-and-entra-id.png)

**Introduction.** Answers "What is an Azure account and how do you log in?"

**Technical explanation.** A new environment brings a **Microsoft Entra ID tenant**, a **new user** and the **Tenant Root Group**. A subscription (card-paid, illustrative amount) sits under the root and contains a VM. On the right, **Entra ID (Free license)** lists allowed users (*Bhakua Engineer*, *Tinku, full-time employee*). Users present username, password and OTP, and receive a **token**. The **Gate** represents authentication; the thief and freshers outside lack valid credentials. At the top, **Azure roles** (Owner, Contributor, Reader) are contrasted with **Entra ID roles** (Global Administrator, User Administrator, Privileged Role Administrator).

**Important concepts.** Tenant, first user, root management group; Entra ID as identity provider; credentials in, token out; subscription as a billing container; two distinct role systems.

**Enterprise use case.** A new organization signs up, the first administrator signs in through Entra ID and creates further users. Only valid identities pass the gate; what they can do depends on their roles.

**Key learning points.**
- Identity lives in the tenant; billing lives in the subscription.
- Authentication proves identity, nothing more.
- Azure roles and Entra roles are different things.

> [!TIP]
> **Accuracy note.** "Azure Account" is a teaching label, and signing in does not grant resource access; RBAC does.

---

### Diagram 3: User Access, Scope and Role (RBAC)

![User Access, Scope and RBAC](images/03-user-access-and-rbac.png)

**Introduction.** Turns the Diagram 2 setup into concrete role assignments.

**Technical explanation.** **Tinku** has **Owner** on **HR MG** and **Contributor** on **Sales MG**. HR MG holds a subscription with resource group `babli-rg` and a VM. In Entra ID, **Bhakua Engineer** is Global Administrator and Subscription Owner, **Tinku** is User Administrator and Subscription Owner, and **Babli** is a third allowed user. A note explains Tinku holds User Administrator so he can onboard users. The summary box states that access needs two things: **Scope** and **Role**.

**Important concepts.** Scope + Role; assignment at management group scope; different roles at different scopes for one user; Entra roles beside Azure roles.

**Enterprise use case.** A team lead gets full control of HR resources and build-only access to Sales without touching individual subscriptions, plus User Administrator to onboard teammates.

**Key learning points.**
- A role assignment always has a scope.
- Owner on a management group reaches every subscription beneath it.
- Directory tasks need Entra roles, not Azure roles.

> [!TIP]
> **Accuracy note.** In practice prefer subscription or resource group scope over Owner at a management group.

---

### Diagram 4: Subscription Management and Group-Based Role Assignment

![Subscription Management and Group-Based RBAC](images/04-subscription-and-role-management.png)

**Introduction.** Shows one subscription with environments and group-based access.

**Technical explanation.** **dhondhu-subscription** sits under the Tenant Root Group with `rg-dev`, `rg-qa`, `rg-uat` and `rg-prod`. **Bhakua** (product owner and future architect) is Global Administrator in Entra ID and **Owner** of the subscription. A **DevOps Contributors / Subscription Group** (Mr. Bill, Mr. Gill, Mr. Till) has **Contributor** on the subscription, and an **rg-dev-contributors** group has **Contributor** only on `rg-dev`. The bottom box lists the pattern: group creation, user addition, role assignment on the group.

**Important concepts.** Environment-based resource groups; Owner vs Contributor; Global Administrator vs Owner; group-based RBAC at two scopes.

**Enterprise use case.** A product team shares one subscription. The DevOps team contributes broadly, while a smaller group works only in development. Membership changes alter access without editing role assignments.

**Key learning points.**
- Assign roles to groups, not people.
- Narrower groups get narrower scopes.
- Global Administrator and Owner belong to different systems.

---

### Diagram 5: Microsoft Entra ID Groups and Security Principles

![Microsoft Entra ID Groups and Security Principles](images/05-entra-id-groups-and-security.png)

**Introduction.** Brings the whole access model together in one view.

**Technical explanation.** The left shows a subscription with four environment resource groups. The right shows the Entra tenant with users and groups. **Bhakua Engineer (Global Admin)** is Owner of the subscription. Users are organized by function: **Azure Executive Control Board** (Billu, Tillu, Lillu), **Subscription Contributor** (Dhillu, Killu), **Subscription Reader**, **RG DEV CONTRIBUTORS** (Pillu, Rillu, interns) and a **Security Group** (Inspector). Three principles head the diagram: minimum privileged role, no Owner for everyone (Contributor only), and group-based RBAC.

**Important concepts.** Groups mapped to job functions; security groups for access control; least privilege; Owner reserved for very few; interns limited to `rg-dev`.

**Enterprise use case.** Leads and DevOps engineers get Contributor on the subscription, interns get Contributor only on development, reviewers get Reader. A change in responsibility is a group membership change.

**Key learning points.**
- Design access by job function.
- Smallest role, narrowest scope, assigned through groups.
- Keep Owner rare.

> [!TIP]
> **Accuracy note.** Roles for *Azure Executive Control Board* and *Subscription Reader*, and some arrows for *Inspector*, are not labeled in the diagram, so they are described as shown.

---

## 21. Key Takeaways

1. **Identity and organization are separate.** Entra ID answers *who*; the hierarchy answers *where*.
2. **Authenticate first, authorize second.** A valid token alone grants no resource access.
3. **Tenant ≠ Subscription.** One tenant can be trusted by many subscriptions; each subscription trusts one tenant.
4. **Management groups sit above subscriptions; resource groups sit below.**
5. **Azure RBAC ≠ Entra roles.** One governs resources, the other the directory.
6. **Access = who + role + scope.**
7. **Inheritance flows downward.** Use the narrowest scope that works.
8. **Prefer groups over individuals, Contributor over Owner, least privilege over convenience.**

---

## 22. My Related Medium Articles

I publish my Azure learning notes on Medium. These articles cover the same topics as the diagrams in this repository.

| Article | Topic | Description | Link |
|---|---|---|---|
| **Understanding Azure Management Group Hierarchy: From Tenant Root to Resources** | Management groups and resource hierarchy | The structure of Azure governance from the Tenant Root Group down to individual resources. Related to Diagram 1. | [Read Article](https://medium.com/@aniketkmr484/understanding-azure-management-group-hierarchy-from-tenant-root-to-resources-48b62324e115) |
| **What Happens When You Create an Azure Account? Understanding Entra ID, Authentication & Authorization** | Entra ID, authentication and authorization | What is created with a new Azure account and how signing in differs from being allowed to act. Related to Diagram 2. | [Read Article](https://medium.com/@aniketkmr484/what-happens-when-you-create-an-azure-account-184dd1d4457a) |
| **Azure RBAC Explained: How to Give Users Access to Azure Resources** | Azure RBAC | Giving users access to Azure resources with roles, scopes and role assignments. Related to Diagrams 3 to 5. | [Read Article](https://medium.com/@aniketkmr484/azure-rbac-explained-how-to-give-users-access-to-azure-resources-191cb291cc18) |

**My Medium profile:** [medium.com/@aniketkmr484](https://medium.com/@aniketkmr484)

---

## 23. Official Microsoft Documentation

| Topic | Link |
|---|---|
| Management groups | [What are Azure management groups?](https://learn.microsoft.com/en-us/azure/governance/management-groups/overview) |
| Resource hierarchy | [Resource organization (Cloud Adoption Framework)](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org) |
| Azure RBAC | [What is Azure RBAC?](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview) |
| RBAC scope | [Understand scope for Azure RBAC](https://learn.microsoft.com/en-us/azure/role-based-access-control/scope-overview) |
| RBAC best practices | [Best practices for Azure RBAC](https://learn.microsoft.com/en-us/azure/role-based-access-control/best-practices) |
| Azure vs Entra roles | [Azure roles, Entra roles and classic administrator roles](https://learn.microsoft.com/en-us/azure/role-based-access-control/rbac-and-directory-admin-roles) |
| Microsoft Entra ID | [What is Microsoft Entra ID?](https://learn.microsoft.com/en-us/entra/fundamentals/whatis) |
| Entra built-in roles | [Microsoft Entra built-in roles](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/permissions-reference) |
| Groups | [Learn about groups and access rights](https://learn.microsoft.com/en-us/entra/fundamentals/concept-learn-about-groups) |
| Elevating access | [Elevate access to manage all subscriptions](https://learn.microsoft.com/en-us/azure/role-based-access-control/elevate-access-global-admin) |

> [!NOTE]
> Microsoft occasionally reorganizes documentation URLs. If a link redirects or breaks, search the topic title on Microsoft Learn.

---

## 24. About the Author

**Aniket Kumar**

I document my Azure and DevOps learning through technical articles, original architecture diagrams and practical explanations. I use GitHub and Medium to explain what I study in a clear, structured way.

<p>
  <a href="https://github.com/aniket-devop">
    <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  </a>
  <a href="https://medium.com/@aniketkmr484">
    <img src="https://img.shields.io/badge/Medium-000000?style=for-the-badge&logo=medium&logoColor=white" alt="Medium" />
  </a>
</p>

Spotted a technical inaccuracy or have a suggestion? Please open an issue. Feedback is welcome.
