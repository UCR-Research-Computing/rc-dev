---
title: "Signing in: accounts and identity by service"
kb_id: KB011
topic: General
audience: "All Users"
updated: 2026-02-09
owner: Research Computing
redirect_from:
  - /Knowledge_Base/KB011_Access_Identity.html
---

## 1. Unified Identity Map

We use different authentication methods optimized for each platform. Your **UCR NetID** is the root identity for all.

| Service | Auth Method | Credentials | How to Login |
| :--- | :--- | :--- | :--- |
| **HPCC** | **LDAP** | NetID + Password | `ssh netid@cluster.hpcc.ucr.edu` |
| **Ursa Major (GCP)** | **SSO (Google)** | UCR Email + MFA | `console.cloud.google.com` |
| **Nautilus** | **CILogon** | UCR Institutional ID | Web Portal / `kubectl` Token |
| **ACCESS** | **CILogon** | UCR Institutional ID | `access-ci.org` |
| **ServiceNow** | **SSO (CAS)** | UCR NetID + MFA | `ucrsupport.service-now.com` |

---

## 2. Common Issues

### "Permission Denied" on HPCC
*   **Cause:** Your account may not be added to the Slurm partition or your password expired.
*   **Fix:** Ensure your PI has paid the subscription. Reset UCR password.

### "No Projects Found" in Google Cloud
*   **Cause:** You logged in with `@gmail.com` instead of `@ucr.edu`.
*   **Fix:** Use an Incognito window and sign in with your official university email.

### "CILogon Failed" on Nautilus
*   **Cause:** Institutional Identity mapping issue.
*   **Fix:** Select "University of California, Riverside" from the dropdown list, NOT "Google".
