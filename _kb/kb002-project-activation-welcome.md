---
title: "When your Ursa Major project is ready"
kb_id: KB002
topic: Cloud
audience: "PIs and lab members with a new Ursa Major project"
updated: 2026-08-19
reviewed: 2026-10-04
owner: Research Computing
redirect_from:
  - /Knowledge_Base/KB002_Project_Activation_Welcome.html
---

When your Ursa Major project has been set up, Research Computing sends the PI and named lab members a welcome email with the project ID. This guide covers the first steps.

## 1. Open your project

Sign in at the [Google Cloud console](https://console.cloud.google.com/) with your **@ucr.edu** account (not a personal Gmail account), then choose your project from the project picker.

- **Dashboard:** an overview of the project.
- **IAM and admin:** who has access. The PI approves changes to project membership.

## 2. Set up the Gemini API (if you need it)

To call Gemini models from code, create an API key under **APIs and services > Credentials** in your project.

- Treat the key like a password. Do not put it in code you share or commit to a repository.
- Usage is tied to your PI's project and its tier. Models and services outside the campus pool are recharged. See [KB005: Ursa Major service tiers](../kb005-ursa-major-service-tiers/).

## 3. Watch your spending

If your project carries a recharge, set a budget alert. See [Creating a project budget](../ursa-major-project-budget/).

## 4. Getting help

For billing questions or quota increases, reply to the ticket from your welcome email, or contact research-computing@ucr.edu.
