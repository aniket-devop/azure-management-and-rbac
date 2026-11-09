# Additional Notes: Diagram Annotation Translations

My original diagrams contain annotations written in Hinglish (Hindi in English letters). This page translates them into English. Translations are my best reading of the screenshots; some words are slightly informal on purpose.

Back to the [main README](../README.md).

## Diagram 1: Management Group Hierarchy

| Original annotation | English meaning |
|---|---|
| Management Group is Top Level Container | A management group is a top-level container (more precisely, the Tenant Root Group is the top) |
| Alag alag subscription ko ek saath rakhne ke liye | To keep different subscriptions together |
| Ek Management group ke andar bhi multiple Child Management group ho sakte hai | A management group can contain multiple child management groups |
| Security ko maintain karne ke, Cost ko maintain karne ke, Governance compliance ko maintain karne ke liye ye hierarchy banai jati hai | This hierarchy is created to maintain security, cost and governance/compliance |
| Papa Management Group | The parent (root) management group |

## Diagram 2: Azure Account and Entra ID

| Original annotation | English meaning |
|---|---|
| Azure Account hota kya hai? Isme login kaise karenge? | What is an Azure account? How do we log in to it? |
| Jab aap naya azure ka account banate ho toh kya kya hota hai andar me | When you create a new Azure account, what gets created inside? |
| 1. Entra ID (Tenant ID) | A Microsoft Entra ID tenant |
| 2. Ek naya user aaja hai | A new user comes into existence |
| 3. Ek naya Management group jiska naam tenant root group hota hai vo ban jata hai | A new management group called the Tenant Root Group is created |
| Andar ghusna = Authentication | Entering = authentication |
| Chor / Fresher | Thief / freshers: people without valid credentials who cannot pass the gate |

## Diagram 3: User Access and RBAC

| Original annotation | English meaning |
|---|---|
| Ki Tinku ne users ko onboard kar pae... | So that Tinku can onboard users |
| Kisi bhi user ko agar access dena hai toh 2 chize hai.. | To give access to any user, two things are needed: Scope and Role |

## Diagram 4: Subscription and Role Management

| Original annotation | English meaning |
|---|---|
| Subscription Creation, Management Group Creation, association sab kuch ko mila ke Onboarding and Offboarding bolte hai.. | Subscription creation, management group creation and the association between them, taken together, are called onboarding and offboarding |

## Diagram 5: Entra ID Groups

| Original annotation | English meaning |
|---|---|
| Minimum Privileged role dena hai.. | Give the minimum privileged role |
| Kisi ko owner nahi dena hai, Only Contributor role dena hai.. | Do not give Owner to anyone; give only the Contributor role |
| Group based RBAC use karna hai.. | Use group-based RBAC |

## Notes on the fictional names

Names such as KGF, Rangoli, Dhondhu, Bhakua, Tinku, Babli, Billu, Tillu and Lillu are invented teaching names. They do not refer to real people, companies or subscriptions.

## Before publishing the screenshots

| Image | What to check |
|---|---|
| `03-user-access-and-rbac.png` | The bottom shows an `ssh` command with an IP address. Crop or blur it unless it is a long-gone lab address. Part of the left edge is also cut off, so some annotations are partly hidden. |
| `02-azure-account-and-entra-id.png` | Contains a credit card graphic. Make sure it is a stock or fake image and not a real card. |
| `04` and `05` | Photos used as user avatars. Confirm you are comfortable using them publicly and that you have the right to. |
