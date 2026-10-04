---
title: "Ursa Major (Google Cloud)"
kicker: "Cloud <span class='sep'>|</span> UCR's Google Cloud research program"
description: "Google Cloud projects for UCR research: AI services, cloud-native workloads and long-term archive, under a tiered allocation framework."
status: By review
tags: [Cloud, AI services, Archive, Tiered]
data_levels: P1-P2 by default
owner: "Research Computing"
reviewed: 2026-10-04
governed_by: "Ursa Major guidelines"
governed_url_rel: /kb/ursa-major-guidelines/
redirect_from:
  - /pages/ursa_major.html
  - /pages/ursa-major-ask.html
fit:
  - AI and machine-learning services on Google Cloud, such as Vertex AI and the Gemini API
  - Cloud-native work such as containers, managed databases and analytics
  - Long-term archive of data you must keep but rarely read
  - Workloads that need cloud tools not available on campus
not_fit:
  - Large batch or GPU computing where the HPCC fits (usually lower cost to the lab)
  - Regulated data outside an approved environment (see the secure enclave)
  - Individual accounts not tied to a lab or PI project
  - Projects expecting Research Computing to fund recharged usage
glance:
  - {k: "Who can use it", v: "UCR PIs and their lab members, in a project anchored to the PI"}
  - {k: "How it is allocated", v: "By request and review, under a tiered framework"}
  - {k: "Cost", v: "Some baseline services may carry no recharge under current terms; GPU, high-performance and large-scale work is recharged"}
  - {k: "Data allowed", v: "P1 and P2 by default"}
cta:
  - {label: "Ask about a project", url: "/help/"}
  - {label: "Read the guidelines", url: "/kb/ursa-major-guidelines/"}
---

## What it is

Ursa Major is UCR's research program on Google Cloud, run by Research Computing with ITS. It gives labs Google Cloud projects for work that is a good fit for the cloud: AI and machine-learning services, cloud-native tools, and long-term archive. It complements, rather than replaces, the campus cluster.

## How allocation works

Requests are reviewed against current campus resources, funding and research priorities. Resources fall into tiers:

- **Baseline (pool) tier.** Selected baseline services may be covered by the campus pool, with no recharge to the lab under current terms. Eligibility, the list of covered services, and limits are set out in the [Ursa Major guidelines]({{ '/kb/ursa-major-guidelines/' | relative_url }}) and can change.
- **Recharge tier.** Cloud GPUs, high-performance machine types, large-scale storage, marketplace models and other specialized resources are billed to a lab funding source through ITS.
- **Dedicated agreements.** Very large or multi-year projects may need their own contract with the provider, with ITS oversight.

[KB005: Ursa Major service tiers]({{ '/kb/kb005-ursa-major-service-tiers/' | relative_url }}) describes the tiers in more detail.

{% include fact.html id="cloud_admin_setup" bare=true %} and {% include fact.html id="cloud_admin_annual" bare=true %} administrative fees can apply to recharged cloud accounts. These are set out in the MOU for the account.

## Costs and the campus pool

Funding for any no-recharge tier comes from campus sources and depends on continued funding. Research Computing does not commit to how long any service will remain without recharge. Lab-funded (recharged) use is billed at the rates in your MOU.

## How to get started

[Contact Research Computing]({{ '/help/' | relative_url }}) with a short description of the project, the data involved (and its protection level), and the funding source if recharged work is likely. Student requests are approved by the student's PI and placed in the PI's project.

## Related

- [AI at UCR]({{ '/compute/cloud-and-ai/' | relative_url }}) for campus AI tools and where their terms are published.
- [Cloud archive]({{ '/services/cloud-archive/' | relative_url }}) for long-term data retention in Google Cloud.
- [KB007: Ursa Major recharge workflow]({{ '/kb/kb007-tier2-recharge-workflow/' | relative_url }}) for setting up a recharged project.
