---
title: "RC shared cluster: web portal (desktops and Jupyter in your browser)"
topic: Cloud
description: "Open the RC shared cluster's Open OnDemand portal from your laptop: files, job lists, a full Linux desktop on a compute node (with a GPU if you want one), and JupyterLab."
audience: "Research Computing staff and close collaborators who have been given access to the RC shared cluster"
reviewed: 2026-10-19
owner: Research Computing
unlisted: true
sitemap: false
review_notes:
  - "Unlisted on purpose (2026-10-19): same audience and mechanism as the getting-started page in this set."
  - "Steps follow the portal's live setup as tested on 2026-10-18 and 2026-10-19: Google sign-in limited to ucr.edu accounts, the IAP tunnel on local port 8443, a Desktop session on gpul4 used interactively (NAMD ApoA1 ran on the L4 from the Desktop terminal), and a Jupyter session that refuses requests without its session token."
  - "The VMD app is hidden in the portal until a GUI build of VMD is installed; this page says so."
  - "CHECK: the tunnel command and portal sign-in have been run on macOS only; the Windows steps (same gcloud command in PowerShell or the Cloud SDK shell) are untested."
---

The RC shared cluster has a web portal (Open OnDemand) that runs in your browser. From it you can browse and edit files, watch your jobs, look up software, and start interactive apps that run as Slurm jobs on compute nodes:

* **Desktop**: a full Linux desktop (XFCE) on a compute node. Pick the GPU partition to get an NVIDIA L4.
* **Jupyter**: JupyterLab on a compute node with one of the site Python environments.

You need the same access as for SSH: see [Getting started](../rc-cluster-getting-started/) ("Before you start"). Other guides in this set: [Software](../rc-cluster-software/) and [AI assistant](../rc-cluster-ai-assistant/).

## Open the portal

The portal has no public address. You reach it through Google's IAP tunnel, the same way SSH works.

1. On your laptop, start the tunnel and leave that terminal open:

   ```bash
   gcloud compute start-iap-tunnel ucrslurmcl-ood-001 443 \
     --local-host-port=localhost:8443 --zone us-central1-a --project PROJECT_ID
   ```

   Use port **8443** exactly. Google sign-in sends you back to `localhost:8443`, so another port fails at sign-in.

2. Open [https://localhost:8443/](https://localhost:8443/) in your browser.

3. Your browser warns that the certificate is not trusted. That is expected here: the traffic is already encrypted inside Google's tunnel. Choose Advanced, then Proceed.

4. Sign in with your `NETID@ucr.edu` Google account. Other Google accounts are refused, and so is a UCR account that has no cluster account yet.

To close the portal, stop the tunnel with Ctrl+C. Interactive sessions keep running on the cluster until you end them (below).

If gcloud says your login has expired, run `gcloud auth login` and start the tunnel again. If the page sends you somewhere odd after an update, try a private browser window.

## Files, jobs and modules

* **Files** opens your home directory and scratch: upload, download, edit and move files in the browser.
* **Jobs > Active Jobs** lists your Slurm jobs, with details for each.
* For a shell, use SSH or a terminal inside a Desktop session.
* The **module browser** lists every installed software module and its versions.

## Start a Desktop

1. **Interactive Apps > Desktop.**
2. Pick a partition: `standard` for general work, `gpul4` for the GPU (one NVIDIA L4), `highmem` for large memory.
3. Set cores, hours and screen size, then **Launch**.
4. The session card shows Queued, then Running. Click **Launch Desktop**.

The desktop runs on a compute node. Open a terminal from the desktop menu and use `module load` as you would over SSH. Your home directory and scratch are there.

On `gpul4`, check the GPU first:

```bash
nvidia-smi -L        # should list one NVIDIA L4
```

A GPU desktop can take a few minutes to start, or wait in the queue when Google has no GPU free in the cluster's zone; the card shows Queued until a node is ready.

### Example: NAMD on the GPU from a Desktop terminal

This is the test we ran when setting up the portal. It downloads the standard ApoA1 benchmark and runs it on the L4 in about 10 seconds.

```bash
module load namd/3.0.1
mkdir -p ~/namd-test && cd ~/namd-test
curl -fsSL https://www.ks.uiuc.edu/Research/namd/utilities/apoa1.tar.gz | tar xz
cd apoa1
grep -vE '^(numsteps|outputname)' apoa1.namd > bench.namd
printf 'numsteps 2000\noutputname apoa1-out\nGPUresident on\n' >> bench.namd
namd-run namd3 +p8 +devices 0 +setcpuaffinity bench.namd | tee namd.log
```

It prints `Info: Benchmark time` lines (about 40 ns/day on one L4 in our test) and ends with a `WallClock:` line. Two warnings about a "destination already in the mount point list" are harmless. Clean up with `rm -rf ~/namd-test`.

## Start Jupyter

1. **Interactive Apps > Jupyter.**
2. Pick a Python environment: `python-sci` (scientific and astronomy), `python-gw` (gravitational waves) or `python-ml` (PyTorch; use `gpul4` for the GPU).
3. Pick a partition, cores and hours, then **Launch**, then **Connect to Jupyter**.

Each session has its own token, which the Connect button passes for you. To add packages, see [Software](../rc-cluster-software/).

## End your session

**An interactive session is a Slurm job.** It holds its node, and counts against your project's use, until you end it or its hours run out. Closing the browser tab or the desktop window does not stop it.

To end it: **My Interactive Sessions**, then **Delete** on the session card. The job is cancelled and the node shuts down. Your files stay.

## Not available yet

* **VMD with a graphical window.** The installed VMD is a build without a display, so the VMD app is hidden. It still works for scripted rendering; see [Software](../rc-cluster-software/).
* ParaView and RStudio apps.

## Get help

Email [research-computing@ucr.edu](mailto:research-computing@ucr.edu) with your NetID, the app, the time you launched it and what you saw. The session card's **Session ID** link opens the session's folder, which has its `output.log`.
