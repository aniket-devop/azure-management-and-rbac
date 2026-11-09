# Additional Notes

Supporting notes for the diagrams in the [main README](../README.md).

## English translation of Hinglish annotations

The diagrams are annotated in Hinglish (Hindi written in English letters).

### Diagram 1: Management Group Hierarchy

| Original | English meaning |
|---|---|
| Papa Management Group | Parent management group |
| Alg alg subscription ko ek saath rakhne ke lie | To keep different subscriptions together |
| Ek Management group ke andar bhi multiple Child Management group ho sakte hai | A management group can also contain multiple child management groups |
| Security ko maintain karne ke, Cost ko maintain karne ke lie, Governance compliance ko maintaine karne ke lie ye hierarchy banai jati hai | This hierarchy is created to maintain security, cost, and governance and compliance |

### Diagram 2: Azure Account and Entra ID

| Original | English meaning |
|---|---|
| Azure Account hota kya hai? | What is an Azure account? |
| Isme login kaise karenge? | How will we log in to it? |
| Jab aap naya azure ka account banate ho toh kya kya hota hai andar me | When you create a new Azure account, what gets created inside it? |
| Ek naya user aaja hai | A new user arrives |
| Ek naya Management group jiska naam tenant root group hota hai vo ban jata hai | A new management group named "Tenant Root Group" gets created |
| Andar ghusna = Authentication | Entering inside = Authentication |
| Papa (next to Global Administrator) | Parent / the top-level administrator |
| Chor / Fresher | Thief / newcomers: identities without valid credentials, kept outside the gate |

### Diagram 3: User Access, Scope and Role

| Original | English meaning |
|---|---|
| Ki Tinku nae users ko onboard kar pae... | So that Tinku can onboard users... |
| Kisi bhi user ko agar access dena hai toh 2 chize hai.. | To give any user access, there are 2 things.. (Scope and Role) |

### Diagram 4: Subscription Management

| Original | English meaning |
|---|---|
| Subscription Creation, Management Group Creation, association sab kuch ko mila ke Onboarding and Offboarding bolte hai.. | Subscription creation, management group creation and the association between them, taken together, are called onboarding and offboarding |

### Diagram 5: Entra ID Groups

| Original | English meaning |
|---|---|
| Minimum Privileged role dena hai.. | Give the minimum privileged role |
| Kisi ko owner nahi dena hai, Only Contributor role dena hai.. | Do not give Owner to anyone; give only the Contributor role |
| Group based RBAC use karna hai.. | Use group-based RBAC |

## Technical clarifications

| Topic | What the diagram simplifies | What Azure actually does |
|---|---|---|
| Top-level container | "Management Group is Top Level Container" | The Tenant Root Group is the single top-level management group; others sit beneath it |
| Azure Account | Shown as one box | You sign in as an Entra identity; the billing and resource boundary is the subscription |
| Cost | Hierarchy "maintains cost" | Cost is reported per subscription and resource group; management groups provide structure |
| Owner at management group | Tinku is Owner at HR MG | Valid, but broad. Prefer narrower scope in practice |
| Global Administrator | Shown beside Owner | Separate system. Global Administrator has no Azure resource access until it elevates access |

## Checking links

Run from any machine with internet access to confirm the URLs in the README respond:

```bash
grep -oE 'https?://[^) "]+' README.md | sort -u | while read -r u; do
  printf '%s  %s\n' "$(curl -s -o /dev/null -L -w '%{http_code}' "$u")" "$u"
done
```

Medium may return 403 to scripts even when the article is fine; open those in a browser.
