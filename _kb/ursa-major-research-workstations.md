---
title: "Ursa Major research workstations"
topic: Cloud
audience: "Researchers who need a cloud virtual machine"
reviewed: 2026-10-04
owner: Research Computing
redirect_from:
  - /Knowledge_Base/Ursa_Major_Research_Workstations.html
---

An Ursa Major research workstation is a virtual machine (VM) in your lab's Google Cloud project. It suits interactive work that needs more power than a laptop, a specific operating system, or software you want full control over.

* [How to launch a research workstation](../ursa-major-workstation-launch/)
* [How to connect to a research workstation](../ursa-major-workstation-connect/)

## What you can configure

- **Machine size:** CPU count, CPU type and memory, chosen when you create the VM and changeable later.
- **Operating system:** a range of Linux distributions and Windows.
- **Persistent disks:** data on persistent disks stays when the VM is stopped (disk storage is charged while it exists).
- **GPUs:** available on recharged projects only.

## Costs

- Standard general-purpose VMs may be covered by the Ursa Major campus pool under current terms, within limits. See [KB005: Ursa Major service tiers](../kb005-ursa-major-service-tiers/).
- GPUs and high-performance machine types are recharged to a lab funding source. For GPU work at lower cost to the lab, consider the [HPCC](../../services/hpcc/).
- Stop VMs you are not using. A running VM uses resources whether or not you are working on it.

## When a workstation is a good fit

- Interactive analysis, visualization or development.
- Software that needs administrator rights or a specific operating system.
- Short-lived environments for a course, workshop or experiment.

For long batch runs, the [HPCC cluster](../../services/hpcc/) is usually a better fit. [Ask us](../../help/) if you are not sure.
