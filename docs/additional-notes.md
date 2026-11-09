# Additional Notes

Supporting notes for the main [README](../README.md): annotation translations, a glossary, and a log of the technical clarifications made while documenting my original diagrams.

---

## 1. Hinglish Annotation Translations

My diagrams use Hinglish (Hindi written in English letters). This table translates the annotations so the original meaning is preserved.

### Diagram 1: Management Group Hierarchy

| Original annotation | English meaning |
|---|---|
| Management Group is Top Level Container | A management group is a high-level container (see accuracy note below) |
| Alg alag subscription ko ek saath rakhne ke liye | To keep different subscriptions together |
| Ek Management group ke andar bhi multiple Child Management group ho sakte hai | A management group can contain multiple child management groups |
| Security ko maintain karne ke, Cost ko maintain karne ke liye, Governance compliance ko maintain karne ke liye ye hierarchy banai jati hai | This hierarchy is created to maintain security, cost organization, and governance/compliance |
| Papa Management Group | The parent (root) management group |
| Child Management Group | A management group beneath a parent |

### Diagram 2: Azure Account and Microsoft Entra ID

| Original annotation | English meaning |
|---|---|
| Azure Account hota kya hai? Isme login kaise karenge? | What is an Azure account? How do you sign in to it? |
| Jab aap naya Azure ka account banate ho toh kya kya hota hai andar me | When you create a new Azure account, what gets created inside? |
| 1. Entra ID (Tenant ID) | A Microsoft Entra ID tenant is created |
| 2. Ek naya user aa jata hai | A new user (the first identity) is created |
| 3. Ek naya Management group jiska naam tenant root group hota hai vo ban jata hai | A management group named Tenant Root Group is created |
| Andar ghusna = Authentication | "Entering the building" = authentication |
| Gate | The authentication checkpoint (sign-in) |
| Chor, Fresher | Visual metaphors for people who do not pass the gate |

### Diagram 3: User Access and RBAC

| Original annotation | English meaning |
|---|---|
| Ki Tinku nae users ko onboard kar pae... | So that Tinku can onboard users |
| Kisi bhi user ko agar access dena hai toh 2 chize hai.. Scope, Role | To give any user access, two things are needed: Scope and Role |
| Role based Access Control - RBAC | Role-based access control |

### Diagram 4: Subscription and Role Management

| Original annotation | English meaning |
|---|---|
| Subscription Creation, Management Group Creation, association sab kuch mila ke Onboarding and Offboarding bolte hai.. | Subscription creation, management group creation, and the association between them, taken together, is what we call onboarding and offboarding |

### Diagram 5: Entra ID Groups

| Original annotation | English meaning |
|---|---|
| Minimum Privileged role dena hai.. | Give the minimum privileged role |
| Kisi ko owner nahi dena hai, Only Contributor role dena hai.. | Do not give Owner to everyone; give only the Contributor role |
| Group based RBAC use karna hai.. | Use group-based RBAC |

> Note: the Diagram 3 screenshot is partly cropped on the left edge, so two of its annotations are only partially visible. Add the full text here if you have the uncropped original.

---

## 2. Glossary

| Term | Definition |
|---|---|
| **Tenant** | A dedicated instance of Microsoft Entra ID for an organization, identified by a Tenant ID |
| **Tenant Root Group** | The top-level management group of a tenant; every other management group and subscription sits beneath it |
| **Management group** | Container for subscriptions and child management groups, used for governance and access at scale |
| **Subscription** | Billing and access boundary that holds resource groups and resources |
| **Resource group** | Logical container for related resources that share a lifecycle |
| **Security principal** | An identity that can be granted access: user, group, service principal, or managed identity |
| **Role definition** | A named collection of permissions (for example, Contributor) |
| **Scope** | The boundary at which a role assignment applies (management group, subscription, resource group, resource) |
| **Role assignment** | Security principal + role definition + scope |
| **Authentication** | Proving identity (signing in) |
| **Authorization** | Determining what an authenticated identity may do |
| **Token** | A signed credential issued after successful sign-in, which proves identity to Azure |
| **Least privilege** | Granting only the permissions needed, at the narrowest scope, for the shortest time needed |
| **Security group** | An Entra ID group used to manage access for many users together |

---

## 3. Role Quick Reference

### Azure RBAC (resources)

| Role | View | Create/modify resources | Grant access to others |
|---|---|---|---|
| Reader | Yes | No | No |
| Contributor | Yes | Yes | No |
| Owner | Yes | Yes | Yes |

### Microsoft Entra ID (directory)

| Role | Scope of responsibility |
|---|---|
| Global Administrator | Broadest directory administration |
| User Administrator | Users and groups (onboarding) |
| Privileged Role Administrator | Assigning and managing Entra roles |

---

## 4. Technical Clarifications Log

Where the original diagrams simplify Azure behavior, the README explains the precise behavior without discarding the original learning material.

| # | Diagram | Original statement | Clarification |
|---|---|---|---|
| 1 | 1 | "Management Group is Top Level Container" | The **Tenant Root Group** is the top-level management group. Other management groups sit beneath it and above subscriptions |
| 2 | 1 | Hierarchy maintains "cost" | Management groups provide organizational structure; spend is tracked per subscription and resource group (typically with tags and Cost Management) |
| 3 | 2 | "Azure Account" as a single container | Azure's model separates the Entra identity (tenant) from the billing and resource boundary (subscription) |
| 4 | 2 | Subscription cost shown as 18000 Rs. | Treated as an illustrative figure, not pricing |
| 5 | 2, 3 | Azure roles and Entra roles side by side | These are separate systems: Azure roles manage resources, Entra roles manage the directory |
| 6 | 3 | Tinku holds "Owner" on HR MG | Owner at management group scope covers all subscriptions beneath it, which is broad; narrower scopes are preferred in practice |
| 7 | 3 | Tinku is a "User Administrator" so he can onboard users | Correct: creating users is an Entra directory task, not an Azure Owner permission |
| 8 | 3 | Screenshot includes an `ssh` command with an IP address | Consider cropping or blurring before publishing |
| 9 | 4, 5 | Global Administrator plus Owner on the subscription | Global Administrator does not by itself grant Azure resource access; Owner is a separate Azure RBAC assignment |
| 10 | 5 | Role for "Azure Executive Control Board" and "Subscription Reader" not labeled | Documented as unlabeled; no assumptions made |

---

## 5. Placeholders to Complete Before Publishing

- [ ] Medium article titles and URLs (README → *Related Medium Articles*)
- [ ] Medium profile URL (README → *Author / Connect With Me*)
- [ ] (Optional) Uncropped version of Diagram 3
- [ ] (Optional) Review the visible IP address in Diagram 3

---

## 6. Self-Study Questions

1. What are the three things created when a new Azure environment (tenant) is set up?
2. Why can a user sign in successfully and still see no resources?
3. What is the difference between a management group and a resource group?
4. Which role lets someone create users in Entra ID, and why does Azure Owner not?
5. Why is Contributor safer than Owner for engineers?
6. Why is assigning a role to a group better than assigning it to each user?
7. If a role is assigned at a management group, which resources does it reach?
