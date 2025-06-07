# Azure AD B2C × External IdP (Okta) – Quick‑Start Guide

> **Purpose**  ·  Stand up an Azure AD B2C tenant, hook it to an external identity provider (Okta in this demo), and expose a *Sign‑up & Sign‑in* user flow your apps can call.
---

## 📁 Prerequisites

|  Tool / Account                                                   | Why you need it                                          |
| ----------------------------------------------------------------- | -------------------------------------------------------- |
| Azure subscription with Owner rights                              | To create the B2C tenant & App Service settings          |
| Global Admin (or at least **User Flow Admin**) role in the tenant | Enables identity‑provider setup                          |
| Okta developer org                                                | Acts as the external IdP we federate to                  |
| `az` CLI & `bicep` or Terraform (optional)                        | For scripting later, not required for click‑through demo |

---

## 🗺️ High‑Level Flow

```text
        ┌──────────────┐
        │  End User    │
        └─────┬────────┘
              │ Browser redirect to B2C user‑flow
              ▼
┌───────────────────────────┐   Federation   ┌────────────────────┐
│ Azure AD B2C (tenant)     │◀──────────────▶│  Okta  OIDC App     │
│ • Custom policy           │                │  (Authorization Sv) │
│ • Sign‑up & Sign‑in flow  │                └────────────────────┘
└───────────────────────────┘                      ▲
              │   issues ID / Access Token       │ token
              ▼                                   │
      ┌──────────────┐   bearer token   ┌──────────────────┐
      │   Web App    │────────────────▶│ ORG Protected API   │
      └──────────────┘                 └──────────────────┘
```

---

## 🔨 Step-by-Step

Below is a concise click‑through guide (screenshots removed for brevity):

1. **Create** an Azure AD B2C tenant using your organisation’s naming convention.
2. **Switch** to the new tenant, search for **“Azure AD B2C”** in the Azure Portal.
3. **Open** **App registrations** → **+ New registration**.
4. **Name** your app, pick *Accounts in any identity provider* (multi‑tenant), check **openid** + **offline\_access**, then **Register**.
5. In **Certificates & secrets**, create a **client secret** and store it safely.
6. In **Authentication**, add your **Redirect URI** (web or SPA) and enable **Access tokens** + **ID tokens**.
7. Go to **User flows** → **+ New flow** → select **Sign‑up & Sign‑in** (latest version) → call it `B2C_1_SUSI_Okta`.
8. Inside the flow, **Identity providers** → **+ OpenID Connect** → paste Okta **Metadata URL**, **Client ID**, and **Client secret**.
9. **Map claims** (`given_name`, `family_name`, `email`) and disable local accounts if you want Okta‑only auth.
10. Click **Run user flow** to test: you should authenticate via Okta and receive an Azure AD B2C ID token.

## ⚙️ Sample `appsettings.json` Snippet 

```jsonc
// Web front‑end
"AzureAdB2C": {
  "Instance": "https://<TENANT>.b2clogin.com/",            // no trailing slash!
  "Domain":   "<TENANT>.onmicrosoft.com",
  "ClientId": "<redacted-guid>",
  "CallbackPath": "/sso/okta/signin",
  "SignUpSignInPolicyId": "B2C_1_SUSI_Okta",
  "OktaMetadataAddress": "{0}/{1}/B2C_1_SUSI_Okta/v2.0/.well-known/openid-configuration"
}
```
Note: I will be posting with Screnshoots soon with step by step guide ..
---



