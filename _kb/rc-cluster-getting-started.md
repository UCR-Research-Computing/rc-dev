---
title: "RC shared cluster: getting started"
topic: Cloud
description: "Get access to Research Computing's shared Slurm cluster in Google Cloud, connect, pick a partition, use the storage, and run a first job."
audience: "Research Computing staff and close collaborators who have been given access to the RC shared cluster"
reviewed: 2026-10-19
owner: Research Computing
unlisted: true
sitemap: false
review_notes:
  - "Unlisted on purpose (2026-10-19): the shared cluster is open to RC staff and close collaborators only, so this page is shared by link and is kept out of the KB index, related guides, site search and the sitemap, with noindex. Give it a KB number and list it if the cluster opens more widely."
  - "Facts (partitions, storage, connection steps, accounts, idle power-down) follow the cluster's own quick start and system specification as read on 2026-10-19. No prices on purpose (Chuck, 2026-10-19): use is within limits set per project."
  - "The project ID is a placeholder on purpose: the site does not publish project IDs. Research Computing sends it with the access email."
  - "Hardware figures are typed here rather than in _data/facts.yml because the page is unlisted and the cluster's own docs (/apps/docs/QUICKSTART.md, sinfo) are the source of truth; move them to facts.yml if the page is listed."
---

Research Computing runs a shared Slurm cluster in Google Cloud. It is open to Research Computing staff and close collaborators by arrangement, and it is used within limits set per project. This page takes you from "I have access" to a first finished job.

For most batch and GPU work at UCR, the campus [HPCC cluster](../../services/hpcc/) is still the first place to look. Use this cluster when Research Computing has set you up on it.

Other guides in this set (shared by link):

* [Web portal: desktops and Jupyter in your browser](../rc-cluster-web-portal/)
* [Software: modules, Python, containers and databases](../rc-cluster-software/)
* [Use the cluster from an AI assistant](../rc-cluster-ai-assistant/)

## Before you start

Research Computing sets up three things for you. You can't do these yourself:

1. **Cloud access** for your UCR Google account (`NETID@ucr.edu`): permission to sign in to the cluster's machines and to reach them through Google's secure tunnel.
2. **A cluster account.** Your Linux user name is your NetID with `_ucr_edu` added, for example `jsmith_ucr_edu`. There is no password; you sign in with your Google account.
3. **A Slurm account** for your lab, set as your default. If you belong to two labs, you pick the second one per job with `#SBATCH --account=PI_NETID`. A job from someone with no Slurm account is refused.

You need the Google Cloud CLI (`gcloud`) on your laptop. Install it from Google's [install page](https://cloud.google.com/sdk/docs/install), then sign in once:

```bash
gcloud auth login            # pick your NETID@ucr.edu account
```

Research Computing sends you the project ID. Below it appears as `PROJECT_ID`.

## Connect

The cluster has no public addresses. You reach the login node through Google's IAP tunnel:

```bash
gcloud compute ssh ucrslurmcl-slurm-login-001 --zone us-central1-a \
  --project PROJECT_ID --tunnel-through-iap
```

The first time, gcloud makes an SSH key for you and asks for a passphrase. You land in `/home/NETID_ucr_edu`. Run `ursa-status` for the partitions, your jobs and your storage.

If gcloud says your login has expired, run `gcloud auth login` again. UCR sign-ins last a few hours.

Prefer a browser? The [web portal](../rc-cluster-web-portal/) gives you files, jobs, a desktop and Jupyter without SSH.

## The login node is shared

Edit files, submit jobs and check on them there. Build software and run anything heavy through Slurm, even short tests. The `check` partition (below) exists so a quick test never needs the login node.

## Storage

| Path | What it is for | Backed up |
| --- | --- | --- |
| `/home/$USER` | code, scripts, results | no, keep your own copy of anything you can't recreate |
| `/scratch/$USER` (`$SCRATCH`) | big working data | no |
| `/data/shared` | public reference databases, read-only, one `db-*` module each | re-downloadable |
| `/tmp` on a compute node (`$TMPDIR`) | per-job temporary files | no, wiped after the job |
| `$LOCALSCRATCH` (nvmescratch only) | fast local NVMe for one job: copy in, work, copy out | no, wiped |

Home and scratch are shared file systems that every node mounts. Don't download your own copy of a big reference database; see the [software guide](../rc-cluster-software/).

## Partitions

Nodes are created when a job needs them (about a minute and a half) and removed when idle. Snapshot as of October 2026; `sinfo` shows the live layout.

| Partition | Node | Use it for |
| --- | --- | --- |
| `standard` (default) | 16 cores, 124 GB | almost everything |
| `spot` | same nodes as standard | arrays and sweeps that can restart: Google can reclaim a node with about 30 seconds' notice, so add `#SBATCH --requeue` |
| `check` | 2 cores, 15 GB, one node always up, 15-minute limit | testing that modules load and a script starts |
| `highmem` | 32 cores, 496 GB | memory-heavy work |
| `gpul4` | 8 cores, 62 GB, one NVIDIA L4 GPU | GPU work |
| `computehigh` | 22 fast cores, low-latency network | tightly coupled multi-node MPI only |
| `nvmescratch` | 8 cores, 62 GB, 737 GB local NVMe | I/O-heavy single-node jobs |

**Ask for the cores you need.** `standard`, `spot`, `check`, `computehigh` and `nvmescratch` share nodes between jobs. A job that asks for nothing gets one core. Use `--cpus-per-task=N` for threads, `--ntasks=N` for MPI ranks, `--mem=` for memory, or `--exclusive` for the whole node. `highmem` and `gpul4` always give a whole node.

Inside a job, size threads from Slurm (`$SLURM_CPUS_PER_TASK`), not from the machine: `os.cpu_count()` and `nproc --all` count the whole node.

**GPU nodes can queue.** GPU nodes come from one Google Cloud zone. When Google has no L4 free there, a `gpul4` job waits in the queue while Slurm keeps retrying, usually minutes, sometimes longer.

## A first job

Copy an example and submit it:

```bash
cp /apps/examples/01-serial.sbatch .     # also OpenMP, MPI, GPU, array and app examples
sbatch 01-serial.sbatch
squeue -u $USER
```

Or write your own:

```bash
#!/bin/bash
#SBATCH --job-name=first
#SBATCH --partition=standard
#SBATCH --cpus-per-task=4
#SBATCH --time=00:30:00
module load python-sci
python3 -c "import numpy; print(numpy.__version__)"
```

Test it on `check` first, then submit the real run elsewhere:

```bash
sbatch -p check -t 10 first.sbatch
```

Interactive shell on a compute node: `srun -p standard -c 4 -t 1:00:00 --pty bash -l`. Launch MPI programs with `srun`, and add `--exclusive` for multi-node MPI.

After a job ends, `seff JOBID` shows how well it used what it asked for, and `sacct -u $USER` lists your recent jobs.

## Limits and use

Use of the cluster is within limits set per project, agreed with Research Computing. Jobs that ask for whole nodes or GPUs use far more than small shared-node jobs, so ask for what the work needs and end interactive sessions when you are done. If your work outgrows what was agreed, talk to us before scaling up.

## Get help

Email [research-computing@ucr.edu](mailto:research-computing@ucr.edu) with your NetID, the job ID and what you expected. On the login node, `/apps/docs/QUICKSTART.md` has the same facts in short form.
