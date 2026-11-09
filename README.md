<div align="center">

# Azure Management Groups, Subscriptions, Microsoft Entra ID & RBAC

### A visual, architecture-first guide to organizing Azure resources and controlling access at scale

![Microsoft Azure](https://img.shields.io/badge/Microsoft_Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Microsoft Entra ID](https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=for-the-badge)
![Azure RBAC](https://img.shields.io/badge/Azure-RBAC-5E5E5E?style=for-the-badge)
![Governance](https://img.shields.io/badge/Cloud-Governance-107C10?style=for-the-badge)
![Least Privilege](https://img.shields.io/badge/Principle-Least_Privilege-D83B01?style=for-the-badge)
![Documentation](https://img.shields.io/badge/Type-Learning_Documentation-444444?style=for-the-badge)

</div>

---

## Introduction

Every Azure environment, from a single-subscription sandbox to a multi-division enterprise, answers the same two questions:

1. **How are resources organized?** (Tenant Root Group → Management Groups → Subscriptions → Resource Groups → Resources)
2. **Who can do what, and where?** (Microsoft Entra ID identities → Azure RBAC roles → scopes)

This repository is a **personal knowledge base** that documents my understanding of those two questions. It is built around five hand-drawn architecture diagrams that I created while studying Azure, which I also used for educational articles on Medium. Each diagram is preserved exactly as drawn and accompanied by a professional technical explanation, with corrections or clarifications where the original diagram simplifies Azure behavior.

> [!NOTE]
> This is a **learning and documentation repository**. It explains concepts and architecture patterns shown in my diagrams. It does **not** contain deployed infrastructure, Infrastructure-as-Code, or production configuration. The names used in the diagrams (for example *KGF*, *Rangoli*, *Dhondhu*, *Bhakua*) are illustrative teaching names, and the amounts shown (for example *18000 Rs.*) are illustrative placeholders, not real pricing.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [What You Will Learn](#what-you-will-learn)
3. [Repository Structure](#repository-structure)
4. [Azure Architecture Overview](#azure-architecture-overview)
5. [Azure Resource Hierarchy](#azure-resource-hierarchy)
6. [Management Groups Explained](#management-groups-explained)
7. [Parent and Child Management Groups](#parent-and-child-management-groups)
8. [Azure Subscription Management](#azure-subscription-management)
9. [Resource Groups Explained](#resource-groups-explained)
10. [Microsoft Entra ID Explained](#microsoft-entra-id-explained)
11. [Azure RBAC Explained](#azure-rbac-explained)
12. [Azure Roles vs Entra ID Roles](#azure-roles-vs-entra-id-roles)
13. [Scope and Role Assignments](#scope-and-role-assignments)
14. [User Onboarding and Offboarding](#user-onboarding-and-offboarding)
15. [Group-Based RBAC Implementation](#group-based-rbac-implementation)
16. [Security Best Practices](#security-best-practices)
17. [Real-World Enterprise Scenario](#real-world-enterprise-scenario)
18. [Architecture Diagrams & Visual Documentation](#architecture-diagrams--visual-documentation)
19. [Key Takeaways](#key-takeaways)
20. [Related Medium Articles](#related-medium-articles)
21. [References and Learning Resources](#references-and-learning-resources)
22. [Author / Connect With Me](#author--connect-with-me)

---

## Project Overview

| Item | Details |
|---|---|
| **Topic** | Azure Management Groups, Subscriptions, Resource Groups, Microsoft Entra ID, Azure RBAC |
| **Format** | Visual documentation: 5 original architecture diagrams with written explanations |
| **Focus areas** | Resource hierarchy, governance, identity, authentication vs authorization, role assignment, group-based access |
| **Audience** | Cloud and DevOps engineers, students preparing for Azure roles, hiring managers reviewing my learning |
| **Status** | Learning documentation (conceptual). No deployed resources. |
| **Companion content** | Medium articles (see [Related Medium Articles](#related-medium-articles)) |

---

## What You Will Learn

- How an Azure **tenant**, **Tenant Root Group**, **management groups**, **subscriptions**, and **resource groups** fit together.
- Why management groups exist: **security, cost organization, and governance/compliance** at scale.
- The difference between a **Microsoft Entra ID tenant** and an **Azure subscription**.
- The difference between **authentication** (proving who you are) and **authorization** (what you are allowed to do).
- How **Azure RBAC roles** (Owner, Contributor, Reader) differ from **Microsoft Entra ID roles** (Global Administrator, User Administrator, Privileged Role Administrator).
- How a **role assignment** is built from *who* + *what role* + *which scope*.
- How **user onboarding and offboarding** connects identity, subscriptions, management groups, and access.
- Why to prefer **group-based RBAC** with **least privilege** over assigning roles directly to individuals.

---

## Repository Structure

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

## Azure Architecture Overview

Azure separates **identity** from **resource organization**. The two layers are connected by **Azure RBAC**.

| Layer | Component | Responsibility |
|---|---|---|
| **Identity plane** | Microsoft Entra ID tenant | Users, groups, authentication, directory roles |
| **Organization plane** | Tenant Root Group → Management Groups → Subscriptions → Resource Groups | Hierarchy for governance, billing boundaries, and resource lifecycle |
| **Access plane** | Azure RBAC | Connects an identity to a role at a scope |

```text
                 ┌───────────────────────────────────┐
                 │   Microsoft Entra ID Tenant       │   ← Identity (who you are)
                 │   users · groups · Entra roles    │
                 └───────────────┬───────────────────┘
                                 │ trusted by
                                 ▼
                 ┌───────────────────────────────────┐
                 │   Tenant Root Group (root MG)     │   ← Organization (where things live)
                 │   └─ Management Groups            │
                 │       └─ Subscriptions            │
                 │           └─ Resource Groups      │
                 │               └─ Resources        │
                 └───────────────┬───────────────────┘
                                 │
                       Azure RBAC role assignment      ← Access (what you can do, and where)
```

> [!IMPORTANT]
> **Authentication** (signing in to Microsoft Entra ID and receiving a token) happens *before* **authorization** (Azure RBAC deciding what that identity may do). This is the "Gate" metaphor used in Diagrams 2 and 3.

---

## Azure Resource Hierarchy

Azure resources are organized in a strict, inheritable hierarchy:

| Level | What it is | Typical purpose |
|---|---|---|
| **Tenant Root Group** | The top-level management group created for the Microsoft Entra tenant | Apply governance and access that must reach every subscription |
| **Management Group** | Container for subscriptions and other management groups | Group subscriptions by business unit, environment, or compliance need |
| **Subscription** | Billing and access boundary; also a scale unit and a limits/quotas boundary | Isolate workloads, teams, or environments |
| **Resource Group** | Logical container for related resources that share a lifecycle | Group resources deployed and managed together |
| **Resource** | An individual service instance (VM, storage account, and so on) | The actual workload components |

**Inheritance:** access and governance settings applied at a higher level flow **downward** to everything beneath it. A role assigned at a management group applies to all child management groups, subscriptions, resource groups, and resources under it.

---

## Management Groups Explained

A **management group (MG)** is a container **above subscriptions** that lets you apply governance and access control to many subscriptions at once.

- Management groups let you **group multiple subscriptions together** and manage them consistently.
- A management group can contain **subscriptions and other (child) management groups**.
- Role assignments and Azure Policy applied to a management group are **inherited** by everything below it.
- Their purpose, as noted in my Diagram 1, is to help maintain **security, cost organization, and governance/compliance**.

> [!NOTE]
> **Clarification of my original diagram.** Diagram 1 describes a management group as the "top level container." Precisely, the **Tenant Root Group** is the top-level management group in a tenant. Other management groups sit beneath it, and subscriptions sit beneath management groups. Management groups also do not hold resources directly; they hold subscriptions and child management groups.
>
> For **cost**, management groups provide the *organizational structure* (for example, rolling up related subscriptions), while actual spend is tracked per subscription and resource group, typically with tags and Microsoft Cost Management.

### Management Group vs Resource Group

| Aspect | Management Group | Resource Group |
|---|---|---|
| Contains | Subscriptions and child management groups | Azure resources |
| Sits | **Above** subscriptions | **Below** subscriptions |
| Primary purpose | Governance and access at scale across subscriptions | Lifecycle management of related resources |
| Holds resources directly? | No | Yes |
| Typical example | `HR MG`, `Sales MG` | `rg-dev`, `rg-prod` |

---

## Parent and Child Management Groups

My Diagram 1 uses a family analogy: the root is the "Papa" (parent) management group, and the groups below it are children.

```text
Tenant Root Group                  ← parent (root)
├── HR Management Group            ← child of root, parent of the groups below it
│   ├── Recruitment HR Team        ← child management group
│   │   └── Recruitment Sub
│   │       └── Resource Group → VMs
│   └── Rangoli Subscription
│       └── Resource Group → VMs
│
└── Sales Management Group         ← child of root
    ├── Sales Subscription
    │   └── Resource Group → VMs
    └── Ads Subscription
        └── Resource Group → VMs
```

**Key rules to remember**

| Rule | Explanation |
|---|---|
| One parent per container | Each management group (except the root) and each subscription has exactly **one** parent management group |
| Multiple children allowed | A management group can have multiple child management groups and subscriptions |
| Depth is limited | The hierarchy supports up to **six levels** of depth (excluding the root level and the subscription level) |
| Root cannot be removed | The Tenant Root Group cannot be deleted or moved |
| Inheritance flows down | Anything assigned at a parent applies to all descendants |

---

## Azure Subscription Management

A **subscription** is the unit that ties resources to **billing** and provides an **access and limits boundary**.

- A subscription is **trusted by exactly one** Microsoft Entra ID tenant. It uses that tenant for authentication and RBAC identities.
- One tenant can have **many** subscriptions.
- In Diagrams 2 and 3, the subscription is paid for with a payment method (credit card icon), and the amount shown (18000 Rs.) is an **illustrative** figure.
- New subscriptions are placed under the **Tenant Root Group** by default and can then be associated with a different management group.

### Azure Subscription vs Microsoft Entra ID Tenant

| Aspect | Microsoft Entra ID Tenant | Azure Subscription |
|---|---|---|
| Role | Identity directory (users, groups, apps) | Billing and resource container |
| Identified by | Tenant ID | Subscription ID |
| Contains | Identities and directory objects | Resource groups and resources |
| Governed by | Entra ID roles (for directory tasks) | Azure RBAC (for resource tasks) |
| Relationship | One tenant → many subscriptions | Each subscription → one trusted tenant |

> [!NOTE]
> **"Azure Account" terminology.** Diagram 2 uses "Azure Account - KGF" as a friendly container for the whole setup. In Microsoft's terminology, what you sign up with is an *identity in a Microsoft Entra tenant*, and the billing and resource boundary is the *subscription*. When a new Azure environment is created, three things come together, exactly as listed in the diagram: **(1)** a Microsoft Entra ID tenant (Tenant ID), **(2)** a user (the first identity), and **(3)** the Tenant Root Group management group.

---

## Resource Groups Explained

A **resource group** is a logical container that holds related Azure resources.

- Every resource belongs to **exactly one** resource group.
- Resources in a resource group usually share a **lifecycle** (deployed, updated, and deleted together).
- A resource group is created **inside a subscription**.
- RBAC can be assigned at resource group scope to give narrow access (for example, only the development environment).

In my diagrams, resource groups are used to separate environments within a subscription: `rg-dev`, `rg-qa`, `rg-uat`, `rg-prod`, plus workload-specific groups such as `babli-rg`.

---

## Microsoft Entra ID Explained

**Microsoft Entra ID** (formerly Azure Active Directory) is Microsoft's cloud identity and access management service. It is the **identity provider** for Azure.

- A **tenant** is a dedicated, isolated instance of Entra ID for an organization, identified by a **Tenant ID**.
- It stores **users** and **groups**, and handles **authentication**.
- The diagrams show a tenant on the **Free** license, which supports basic user and group management.

### Authentication vs Authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question answered | *Who are you?* | *What are you allowed to do?* |
| Handled by | Microsoft Entra ID | Azure RBAC (for Azure resources) |
| Inputs | Username/password, OTP/MFA | Role + scope |
| Result | A **token** proving identity | Allowed or denied actions |
| Diagram metaphor | "Andar ghusna = Authentication" (*entering the building*; the **Gate**) | Permissions once inside |

The login flow shown in Diagrams 2 and 3:

1. A user visits `portal.azure.com`.
2. The user signs in with **username/password/OTP** against Microsoft Entra ID (**the Gate**).
3. On success, the user receives a **token**.
4. Azure uses the token's identity, then evaluates **RBAC role assignments** to decide what the user can do.

> [!NOTE]
> An Entra user can sign in successfully and still see **nothing** in Azure. Authentication succeeds, but without an RBAC role assignment there is no authorization to any resources.

---

## Azure RBAC Explained

**Azure role-based access control (RBAC)** is the authorization system for Azure resources. As Diagram 3 summarizes: *to give any user access, you need two things, **Scope** and **Role**.*

A **role assignment** has three parts:

| Part | Question | Example from my diagrams |
|---|---|---|
| **Security principal** | Who? | A user (Tinku), or a group (DevOps Contributors) |
| **Role definition** | What can they do? | Owner, Contributor, Reader |
| **Scope** | Where does it apply? | Management group, subscription, or resource group |

### Core built-in Azure roles

| Role | Manage resources | Assign roles to others | Typical use |
|---|---|---|---|
| **Owner** | Full access | **Yes** | Very few trusted administrators |
| **Contributor** | Create and manage all resource types | **No** | Engineers and DevOps teams |
| **Reader** | View only | No | Auditors, stakeholders, interns |

> [!IMPORTANT]
> **Contributor cannot grant access.** Only roles with role-assignment permissions (such as Owner, or User Access Administrator) can create role assignments. This is why Contributor is the safer default for engineers.

---

## Azure Roles vs Entra ID Roles

These are two **separate** role systems. Both appear side by side at the top of Diagrams 2 and 3.

| Aspect | Azure RBAC Roles | Microsoft Entra ID Roles |
|---|---|---|
| **Manage what?** | Azure **resources** (VMs, networks, storage, and so on) | The **directory**: users, groups, app registrations, tenant settings |
| **Examples** | Owner, Contributor, Reader | Global Administrator, User Administrator, Privileged Role Administrator |
| **Scope** | Management group, subscription, resource group, resource | Tenant (or an administrative unit) |
| **Managed in** | Access control (IAM) blade of a resource, resource group, subscription, or management group | Microsoft Entra admin center, Roles and administrators |
| **Who can assign** | Owner or User Access Administrator at that scope | Global Administrator or Privileged Role Administrator |

### Entra ID roles shown in the diagrams

| Entra ID role | Purpose |
|---|---|
| **Global Administrator** | Highest directory privilege; manages almost all aspects of Entra ID |
| **User Administrator** | Creates and manages users and groups (this is the role relevant to onboarding users) |
| **Privileged Role Administrator** | Manages role assignments in Entra ID (shown as "Privileged role admin") |

> [!WARNING]
> **Do not assume Entra roles grant Azure resource access.** A Global Administrator does **not** automatically have access to Azure subscriptions. A Global Administrator can *elevate access* to manage all subscriptions and management groups in the tenant, but that is a deliberate, auditable action. Conversely, an Azure **Owner** cannot create users in Entra ID unless they also hold an appropriate Entra role.

---

## Scope and Role Assignments

**Scope** is the set of resources that an access assignment applies to. Azure scopes are hierarchical, and assignments **inherit downward**:

```text
Management Group      ← broadest
   └─ Subscription
        └─ Resource Group
             └─ Resource    ← narrowest
```

| Scope | Effect of assigning Contributor at this level |
|---|---|
| Management group | Contributor on **all subscriptions** beneath it |
| Subscription | Contributor on **all resource groups and resources** in that subscription |
| Resource group | Contributor only on resources **in that resource group** |
| Resource | Contributor only on that single resource |

### Examples demonstrated in my diagrams

| Diagram | Assignment shown |
|---|---|
| 3 | **Tinku** → *Owner* at **HR MG** scope, and *Contributor* at **Sales MG** scope |
| 4 | **Bhakua** → *Owner* at **dhondhu-subscription** scope |
| 4 | **DevOps Contributors** group → *Contributor* at **subscription** scope |
| 4 | **rg-dev-contributors** group → *Contributor* at **rg-dev** resource group scope |
| 5 | **Bhakua Engineer (Global Admin)** → *Owner* at **subscription** scope |

> [!TIP]
> Always choose the **narrowest scope** that lets someone do their job. A developer who only works in `rg-dev` should get access at `rg-dev`, not at the subscription.

---

## User Onboarding and Offboarding

Diagram 4 states that **subscription creation, management group creation, and the association between them, taken together, make up onboarding and offboarding** of a workload or team.

A practical view of the lifecycle:

| Stage | Identity (Microsoft Entra ID) | Access (Azure RBAC) | Organization |
|---|---|---|---|
| **Onboarding** | Create the user (needs a role such as User Administrator) | Add the user to the correct **group**; the group already carries the role at the right scope | Place the subscription under the correct management group |
| **Role change** | Move the user between groups | Access changes automatically through group membership | No change |
| **Offboarding** | Remove the user from groups; disable or delete the account | Access disappears with group membership | Workload subscription can be moved or retired if the team is dissolved |

> [!NOTE]
> This is why Diagram 3 includes the note *"so that Tinku can onboard users."* Onboarding users is an **Entra ID directory task**, so it requires an Entra role such as **User Administrator**. An Azure *Owner* assignment alone does not provide it.

---

## Group-Based RBAC Implementation

Diagram 4 shows a three-step pattern, which Diagram 5 expands into an organization-wide design:

```text
1. Group creation               → create a security group in Microsoft Entra ID
2. User addition in group       → add the people who need the same access
3. Role assignment on group     → assign the Azure role to the group at the right scope
```

### Why group-based RBAC?

| Direct user assignments | Group-based assignments |
|---|---|
| One assignment per person per scope | One assignment per group per scope |
| Hard to audit as the team grows | Easy to review: "who is in this group?" |
| Offboarding means hunting down every assignment | Offboarding means removing group membership |
| Role sprawl and mistakes | Consistent, repeatable access model |

### Groups shown in the diagrams

| Group | Members shown | Role / scope shown |
|---|---|---|
| **DevOps Contributors / Subscription Group** (Diagram 4) | Mr. Bill, Mr. Gill, Mr. Till | Contributor on `dhondhu-subscription` |
| **rg-dev-contributors** (Diagram 4) | *(members not shown)* | Contributor on `rg-dev` |
| **Azure Executive Control Board** (Diagram 5) | Mr. Billu (Architect), Mr. Tillu (Manager), Mr. Lillu (Architect2) | *(role not labeled in the diagram)* |
| **Subscription Contributor** (Diagram 5) | Mr. Dhillu (Lead), Mr. Killu (DevOps) | Contributor at subscription scope (by name) |
| **Subscription Reader** (Diagram 5) | *(members not shown)* | Reader at subscription scope (by name) |
| **RG DEV CONTRIBUTORS** (Diagram 5) | Mr. Pillu (Intern), Mr. Rillu (Intern) | Contributor at `rg-dev` scope (by name) |
| **SECURITY GROUP** (Diagram 5) | Inspector | Security group; role not labeled in the diagram |

> [!NOTE]
> Where the diagram does not label a role or members, this table says so rather than guessing. The group naming itself (*Subscription Contributor*, *RG DEV CONTRIBUTORS*) indicates the intended role and scope.

---

## Security Best Practices

The three principles written at the top of Diagram 5 are the foundation of a sound access model:

| Principle (from Diagram 5) | Professional interpretation |
|---|---|
| *"Minimum privileged role dena hai"* | Apply the **principle of least privilege**: grant only the permissions required for the task |
| *"Kisi ko Owner nahi dena hai, only Contributor role dena hai"* | **Limit Owner** to a very small, trusted set of administrators; give engineers **Contributor** |
| *"Group-based RBAC use karna hai"* | Assign roles to **groups**, not individuals |

**Additional recommended practices** (general Azure guidance, beyond what the diagrams show):

- ✅ Assign access at the **narrowest scope** that works (resource group before subscription before management group).
- ✅ Keep **Global Administrator** assignments minimal, and separate day-to-day identities from highly privileged ones.
- ✅ Use **MFA** for administrative accounts.
- ✅ Prefer **just-in-time** privileged access (for example, Microsoft Entra Privileged Identity Management, subject to licensing) over permanent elevated roles.
- ✅ Review role assignments and group membership **regularly** (access reviews).
- ✅ Keep **at least two** trusted owners/administrators so access is not dependent on a single person.
- ✅ Separate environments (`dev`, `qa`, `uat`, `prod`) with different access levels. Production should be the most restricted.
- ✅ Use **Azure Policy** on management groups for guardrails such as allowed regions or required tags.

> [!NOTE]
> Diagram 5 shows **one Owner** (the Global Administrator engineer) at the subscription scope. This is consistent with "very few owners." In real environments, teams commonly keep more than one for continuity and use privileged-access controls around them.

---

## Real-World Enterprise Scenario

The scenario below is a **conceptual design** built from the structure in Diagram 1 and the access model in Diagrams 4 and 5. It is an illustration, not a deployed environment.

### Organization structure

```text
Tenant Root Group
│
├── HR Management Group
│   ├── Recruitment HR Team (child Management Group)
│   │   └── Recruitment Subscription
│   │       └── Recruitment Resource Group(s)
│   └── Rangoli Subscription
│       └── HR Resource Group(s)
│
└── Sales Management Group
    ├── Sales Subscription
    │   └── Sales Resource Group
    └── Ads Subscription
        └── Ads Resource Group
```

### How each component contributes

| Component | Role in the design |
|---|---|
| **Tenant Root Group** | Top of the hierarchy; used sparingly for organization-wide governance |
| **HR / Sales Management Groups** | Separate business units, so each can have its own governance and access rules |
| **Subscriptions** | Billing and isolation boundaries per team or workload (Recruitment, Rangoli, Sales, Ads) |
| **Resource Groups** | Group resources by workload or environment (for example `rg-dev`, `rg-prod`) |
| **Microsoft Entra ID users** | The identities of employees and administrators |
| **Security groups** | Collect users by function (for example, "Sales Subscription Contributors") |
| **Azure RBAC** | Binds each group to a role at the right scope |

### Example access design (conceptual)

| Who (Entra ID group) | Role | Scope | Why |
|---|---|---|---|
| Sales cloud engineers | Contributor | Sales Subscription | Build and manage Sales workloads, but cannot grant access |
| Ads engineers | Contributor | Ads Resource Group | Narrower access, limited to the Ads workload |
| Auditors / stakeholders | Reader | HR Management Group | View-only across all HR subscriptions |
| Cloud platform admins (very few) | Owner | Subscription or management group | Manage access and governance |
| Identity administrators | *Entra role:* User Administrator | Tenant | Onboard and manage users and groups |

### The flow end to end

1. A new Sales engineer joins. An identity administrator (User Administrator) **creates the user** in Entra ID.
2. The user is **added to the Sales engineers security group**.
3. The group already holds **Contributor on the Sales Subscription**, so access is granted without a per-user role assignment.
4. The user signs in at `portal.azure.com` (**authentication**), receives a token, and Azure evaluates RBAC (**authorization**).
5. When the engineer leaves, **removing them from the group** (and disabling the account) removes their access.

---

## Architecture Diagrams & Visual Documentation

> [!NOTE]
> These are my **original diagrams**, used unmodified. Annotations in the diagrams are written in Hinglish (Hindi written in English letters). Each section below explains them in professional English. A line-by-line translation of the annotations is available in [`docs/additional-notes.md`](docs/additional-notes.md).

---

### Diagram 1: Azure Management Group Hierarchy

![Management Group Hierarchy](images/01-management-group-hierarchy.png)

**Technical explanation**

This diagram shows how Azure resources are organized beneath the **Tenant Root Group**. Two child management groups, **HR** and **Sales**, hang from the root. HR contains a further child management group (**Recruitment HR Team**) and a subscription (**Rangoli**). Sales contains two subscriptions (**Sales** and **Ads**). Each subscription contains a **resource group**, and each resource group contains **virtual machines**. A user (**Dhondhu**) is shown connected to the Ads resource group.

The annotations summarize why this structure exists: management groups group multiple subscriptions together, a management group can contain multiple child management groups, and the hierarchy exists to maintain **security, cost organization, and governance/compliance**.

**Important concepts demonstrated**

- Tenant Root Group (parent / "Papa" management group)
- Parent and child management groups
- Multiple subscriptions grouped under management groups
- Subscription → resource group → resource nesting
- Inheritance of governance from top to bottom
- Access granted at a narrow level (a user connected to one resource group)

**Real-world use case**

A company separates **HR** and **Sales** into their own management groups. The compliance team applies HR-specific policies at the HR management group, and they automatically cover both the Recruitment and Rangoli subscriptions. Sales teams manage Sales and Ads separately, but under one governance umbrella.

> [!TIP]
> **Accuracy note.** The Tenant Root Group is the true top-level container; management groups are not the very top. See [Management Groups Explained](#management-groups-explained).

---

### Diagram 2: Azure Account Structure, Microsoft Entra ID and the Authentication Flow

![Azure Account and Microsoft Entra ID](images/02-azure-account-and-entra-id.png)

**Technical explanation**

This diagram answers *"What is an Azure account, and how do you log in?"* When a new Azure environment is created, three things appear: a **Microsoft Entra ID tenant (Tenant ID)**, a **new user**, and a **Tenant Root Group** management group. A subscription (paid by card, shown with an illustrative amount) is placed under the Tenant Root Group and contains a virtual machine.

On the right, **Microsoft Entra ID (Free license)** holds the **allowed users** (*Bhakua Engineer* and *Tinku, full-time employee*). A user signs in with **username/password/OTP**, and Entra ID issues a **token**. The **Gate** and the note *"Andar ghusna = Authentication"* represent authentication as the entry point; people without valid credentials (shown as a thief and "Freshers" waiting outside) do not get in.

At the top, the diagram contrasts **Azure roles** (Owner, Contributor, Reader) with **Entra ID roles** (Global Administrator, User Administrator, Privileged Role Administrator).

**Important concepts demonstrated**

- What is created with a new Azure environment (tenant, first user, Tenant Root Group)
- Microsoft Entra ID as the identity provider
- Authentication: credentials in, token out
- Subscription as a billing container under the root management group
- Azure roles vs Entra ID roles (two distinct systems)

**Real-world use case**

A new organization signs up for Azure. The first administrator signs in through Entra ID, sees the tenant structure, and later creates additional users. Only people with valid identities can pass the gate; what they can do afterwards depends on their assigned roles.

> [!TIP]
> **Accuracy note.** "Azure Account" is a teaching label. In Azure, the identity lives in the Entra tenant, and billing lives in the subscription. Signing in does not grant resource access; RBAC does.

---

### Diagram 3: User Access, Scope and Role (RBAC)

![User Access and RBAC](images/03-user-access-and-rbac.png)

**Technical explanation**

This diagram builds on Diagram 2 and shows real role assignments. Under the Tenant Root Group are **HR MG** and **Sales MG**. The user **Tinku** is shown with **Owner** on the HR MG and **Contributor** on the Sales MG. The HR MG contains a subscription, which contains the resource group `babli-rg` with a virtual machine.

On the Entra ID side, the allowed users are **Bhakua Engineer** (Global Administrator and Subscription Owner), **Tinku** (User Administrator and Subscription Owner), and **Babli**. The note *"so that Tinku can onboard users"* explains why Tinku holds the User Administrator role. The bottom summary states the central RBAC idea: to give any user access you need **two things: Scope and Role**.

**Important concepts demonstrated**

- Scope + Role = Azure RBAC
- Role assignment at **management group** scope
- Different roles at different scopes for the same user
- Entra ID roles (directory) alongside Azure roles (resources)
- User Administrator as the role for onboarding users

**Real-world use case**

A team lead needs full control over HR resources and limited, build-only access to Sales resources. Assigning Owner on the HR MG and Contributor on the Sales MG achieves this without touching individual subscriptions. Giving the same person the User Administrator Entra role lets them onboard team members.

> [!WARNING]
> **Privacy note about this screenshot.** The bottom of this image contains an `ssh` command with a **visible IP address**. If that IP belongs to a real system, consider cropping or blurring it before publishing. If it is only a lab or sandbox address that is already torn down, this is lower risk, but cropping is still good practice.

> [!TIP]
> **Accuracy note.** Owner at a management group is very broad because it covers *every* subscription beneath it. In practice, prefer subscription or resource group scope. Owner also does not provide directory permissions such as creating users; that is the Entra role's job.

---

### Diagram 4: Subscription Management and Group-Based Role Assignment

![Subscription and Role Management](images/04-subscription-and-role-management.png)

**Technical explanation**

This diagram shows a subscription named **dhondhu-subscription** under the Tenant Root Group, containing four resource groups: **rg-dev, rg-qa, rg-uat, rg-prod**. **Bhakua**, the product owner and future architect, holds **Global Administrator** in Microsoft Entra ID and **Owner** on the subscription.

Instead of assigning roles to each engineer, a **"DevOps Contributors / Subscription Group"** (Mr. Bill, Mr. Gill, Mr. Till) receives **Contributor** on the subscription, and a separate **rg-dev-contributors** group receives **Contributor** only on **rg-dev**. The bottom box lists the three-step pattern: **group creation → user addition in group → role assignment on group**. The top note explains that subscription creation, management group creation, and the association between them together make up **onboarding and offboarding**.

**Important concepts demonstrated**

- Subscription containing environment-based resource groups
- Owner vs Contributor at subscription scope
- Entra ID Global Administrator vs Azure Owner (different systems)
- Group-based RBAC at subscription and resource group scope
- The group workflow: create group, add users, assign role to group

**Real-world use case**

A product team works in one subscription with separate environments. The wider DevOps team can contribute across the subscription, while a smaller group only works in the development resource group. Adding or removing a person from a group immediately changes what they can do.

> [!TIP]
> **Accuracy note.** The users in this diagram share one avatar image, so the groups should be read by their names and labels, not by the people pictured.

---

### Diagram 5: Microsoft Entra ID Groups and Security Principles

![Entra ID Groups and Security](images/05-entra-id-groups-and-security.png)

**Technical explanation**

This diagram brings the model together. The left side shows an Azure account with a subscription containing `rg-dev`, `rg-qa`, `rg-uat`, and `rg-prod`. The right side shows the Microsoft Entra ID tenant with **users** and **groups**. **Bhakua Engineer (Global Admin)** holds **Owner** on the subscription.

Users are organized by function:

- **Azure Executive Control Board:** Billu (Architect), Tillu (Manager), Lillu (Architect2)
- **Subscription Contributor:** Dhillu (Lead), Killu (DevOps)
- **Subscription Reader**
- **RG DEV CONTRIBUTORS:** Pillu and Rillu (Interns)
- **SECURITY GROUP:** Inspector

The three principles at the top of the diagram are the key message: **minimum privileged role, no Owner for everyone (Contributor only), and group-based RBAC.**

**Important concepts demonstrated**

- Entra ID groups mapped to job functions
- Security groups used for access control
- Least privilege
- Owner reserved for a very small number of administrators
- Interns scoped to the development resource group only
- Role granularity by scope (subscription vs resource group)

**Real-world use case**

An organization maps job functions to access levels. Leads and DevOps engineers get Contributor on the subscription, interns get Contributor only on the development resource group, and reviewers get Reader. When someone's responsibilities change, an administrator changes group membership rather than editing individual role assignments.

> [!TIP]
> **Accuracy note.** The role assigned to *Azure Executive Control Board* and *Subscription Reader* is not labeled in the diagram, and the exact membership arrows for the *Inspector* are not fully clear. They are described here as shown, without assumptions.

---

## Key Takeaways

1. **Identity and organization are separate.** Entra ID handles *who you are*; the management group → subscription → resource group hierarchy handles *where resources live*.
2. **Authentication comes first, authorization second.** A valid token alone does not grant resource access.
3. **Tenant ≠ Subscription.** One tenant can trust many subscriptions; each subscription trusts one tenant.
4. **Management groups sit above subscriptions; resource groups sit below.** They solve different problems.
5. **Azure RBAC ≠ Entra ID roles.** Azure roles control resources; Entra roles control the directory.
6. **Access = Scope + Role (+ who).** Always ask: *Who? What role? Where?*
7. **Inheritance flows downward.** Assign at the narrowest scope that works.
8. **Prefer groups over individuals, Contributor over Owner, and least privilege over convenience.**

---

## Related Medium Articles

> [!NOTE]
> Replace the placeholders below with your real article titles and URLs once confirmed. No Medium URLs have been invented.

| Article Title | Topic | Medium Article URL |
|---|---|---|
| `[PLACEHOLDER: Article title]` | Azure Management Groups and resource hierarchy | `[PLACEHOLDER: https://medium.com/@your-handle/article-slug]` |
| `[PLACEHOLDER: Article title]` | Azure account, Microsoft Entra ID and authentication | `[PLACEHOLDER: https://medium.com/@your-handle/article-slug]` |
| `[PLACEHOLDER: Article title]` | Azure RBAC: scope, roles and role assignments | `[PLACEHOLDER: https://medium.com/@your-handle/article-slug]` |
| `[PLACEHOLDER: Article title]` | Subscription management and group-based RBAC | `[PLACEHOLDER: https://medium.com/@your-handle/article-slug]` |
| `[PLACEHOLDER: Article title]` | Microsoft Entra ID groups, least privilege and security | `[PLACEHOLDER: https://medium.com/@your-handle/article-slug]` |

---

## References and Learning Resources

Official Microsoft documentation:

| Topic | Link |
|---|---|
| Management groups overview | [Microsoft Learn: Management groups](https://learn.microsoft.com/azure/governance/management-groups/overview) |
| Azure Resource Manager and resource groups | [Microsoft Learn: Azure Resource Manager overview](https://learn.microsoft.com/azure/azure-resource-manager/management/overview) |
| Organizing subscriptions | [Cloud Adoption Framework: Organize subscriptions](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions) |
| Azure RBAC overview | [Microsoft Learn: What is Azure RBAC?](https://learn.microsoft.com/azure/role-based-access-control/overview) |
| Azure RBAC best practices | [Microsoft Learn: RBAC best practices](https://learn.microsoft.com/azure/role-based-access-control/best-practices) |
| Azure roles vs Entra roles | [Microsoft Learn: Azure roles, Entra roles and classic administrator roles](https://learn.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) |
| Microsoft Entra ID | [Microsoft Learn: What is Microsoft Entra ID?](https://learn.microsoft.com/entra/fundamentals/whatis) |
| Entra built-in roles | [Microsoft Learn: Entra ID built-in roles](https://learn.microsoft.com/entra/identity/role-based-access-control/permissions-reference) |
| Entra groups | [Microsoft Learn: Learn about groups and access rights](https://learn.microsoft.com/entra/fundamentals/concept-learn-about-groups) |
| Elevate access for Global Administrators | [Microsoft Learn: Elevate access to manage all subscriptions](https://learn.microsoft.com/azure/role-based-access-control/elevate-access-global-admin) |

> [!NOTE]
> Microsoft occasionally reorganizes documentation URLs. If a link redirects or breaks, search the topic title on Microsoft Learn.

---

## Author / Connect With Me

**Aniket Kumar**

I am building my Azure and DevOps knowledge in public, using diagrams and written explanations to reinforce what I learn.

| Platform | Link |
|---|---|
| 🐙 **GitHub** | [github.com/aniket-devop](https://github.com/aniket-devop) |
| 💼 **LinkedIn** | [linkedin.com/in/aniket484](https://www.linkedin.com/in/aniket484) |
| ✍️ **Medium** | `[PLACEHOLDER: https://medium.com/@your-handle]` |

If you spot a technical inaccuracy or have a suggestion, please open an issue. Feedback is welcome.

---

<div align="center">

⭐ If this repository helped you understand Azure access and governance, consider starring it.

</div>
