---
title: "Creating GCP Budgets with Project Discounts and Credit Alerts"
topic: Cloud
owner: Research Computing
reviewed: 2026-10-04
review_notes:
  - "Rewrote to match the current Budgets & alerts console (scope, Savings, amount, thresholds, role-based email options). Removed screenshot placeholders, marketing text and the closing summary."
  - "Added Ursa Major context: project usage outside Tier 1 is recharged (Tier 2), and a budget sends alerts but does not cap spending."
  - "CHECK: whether Ursa Major project owners can create project budgets themselves under the current billing account setup, or whether Research Computing sets them up."
  - "CHECK: whether Tier 2 recharge is based on cost after credits and discounts, so the advice to include Savings matches what the lab is billed."
redirect_from:
  - /Knowledge_Base/Ursa_Major_Project_Budget_Creation.html
---

A Google Cloud budget watches what your Ursa Major project spends and emails alerts when spending reaches thresholds you set. This guide shows how to create a budget for one project, decide whether discounts and credits count toward it, and send alerts to the project owners.

Most Ursa Major usage, including VMs, GPUs, Standard storage and Filestore, is Tier 2: it is recharged to a lab funding source under an MOU. A budget is a simple way to see that spending early. See [KB005: Ursa Major service tiers](../kb005-ursa-major-service-tiers/) and [KB007: Ursa Major recharge workflow](../kb007-tier2-recharge-workflow/).

**A budget does not stop spending.** It only sends alerts. To limit costs, stop or delete resources you are not using.

## Before you start

- You need to be a Project Owner on the project, or hold a billing role that allows budgets. If the Budgets & alerts page is not available to you, contact [research-computing@ucr.edu](mailto:research-computing@ucr.edu).
- Have the project name ready (for example `my-lab-project`).

## 1. Open Budgets & alerts

1. Go to the [Google Cloud console](https://console.cloud.google.com/) and sign in with your UCR account.
2. Select your project in the project picker at the top of the page.
3. Open the navigation menu and select **Billing**. If asked, choose **Go to linked billing account**.
4. In the **Cost management** section of the Billing menu, select **Budgets & alerts**.
5. Click **Create budget**.

## 2. Set the scope

1. **Name:** enter a descriptive name, for example `my-lab-project monthly budget`.
2. **Time range:** choose the budget period. Monthly is a common choice.
3. **Projects:** confirm only your project is selected.
4. **Services:** leave as all services unless you want to watch one service (for example Compute Engine).
5. **Savings:** choose whether discounts and credits count toward the budget (see the next section).
6. Click **Next**.

## 3. Decide on discounts and credits (Savings)

Savings include discounts and credits that reduce the cost of your usage, such as committed use discounts, sustained use discounts and promotional credits.

- **Include Savings** to track net spending: total cost minus applicable discounts and credits. This is usually the closer match to what is recharged to the lab.
- **Clear all Savings options** to track spending before any discounts and credits are applied.

If credits exceed usage in a period, net spending can show as a negative amount.

## 4. Set the budget amount

1. **Budget type:** choose **Specified amount** and enter the amount the lab expects to spend in each period. You can also base the amount on last period's spend.
2. Click **Next**.

## 5. Set alert thresholds

1. Add threshold rules as a percentage of the budget, for example 50%, 90% and 100%.
2. For each rule, choose **Actual** (spending so far this period) or **Forecasted** (projected spending by the end of the period). A forecasted rule gives earlier warning.

## 6. Choose who gets the alerts

- **Email alerts to billing admins and users** is selected by default and sends alerts to people with billing roles on the billing account.
- **Email alerts to project owners** sends alerts to everyone with the Project Owner role on the project. This option is only available when the budget covers a single project. Select it so the lab's project owners are notified.
- To notify other people, such as a lab manager, link **Cloud Monitoring email notification channels**.
- Advanced users can connect a **Pub/Sub topic** to act on alerts automatically, for example to stop VMs. See [Google's guide to programmatic budget notifications](https://cloud.google.com/billing/docs/how-to/budgets-programmatic-notifications).

## 7. Save

Review the settings and click **Finish**. Check the budget every few months and adjust the amount and thresholds as the lab's work changes.

## More information

- [Google Cloud: Create, edit, or delete budgets and budget alerts](https://cloud.google.com/billing/docs/how-to/budgets)
- [Google Cloud: Cloud Billing concepts](https://cloud.google.com/billing/docs/concepts)
- Questions: [research-computing@ucr.edu](mailto:research-computing@ucr.edu) or see [Get help](../../help/).
