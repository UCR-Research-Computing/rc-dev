---
title: "RC shared cluster: use it from an AI assistant"
topic: Cloud
description: "Connect an AI assistant (Claude, Codex, Gemini CLI, OpenCode, Hermes) to the RC shared cluster's MCP server so it can check the cluster, explain your jobs, look up software, check scripts and, with your approval, submit and cancel your jobs."
audience: "Research Computing staff and close collaborators who have been given access to the RC shared cluster"
reviewed: 2026-10-19
owner: Research Computing
unlisted: true
sitemap: false
review_notes:
  - "Unlisted on purpose (2026-10-19): same audience and mechanism as the getting-started page in this set."
  - "The server's name and address are left out on purpose: the site wording rules do not name this service publicly. Research Computing sends the address (SERVER_URL here) when it adds a person to the server's sign-in list."
  - "Client commands follow the server's client guide, each run against the live server on 2026-10-02; tool names and the two-step approval follow its specification and live tool list. Prices are left out (Chuck, 2026-10-19)."
  - "CHECK: the per-person caps (nodes, hours, submissions per day) are set per person by Research Computing and are not printed here."
---

The RC shared cluster has an MCP server: a service that gives an AI assistant a fixed set of cluster tools. Once your assistant is connected, you can ask in plain language:

* "What's running on the cluster, and is anything queued?"
* "Why did my job 1234 fail?"
* "Which module has GROMACS, and what does it need loaded first?"
* "Check this batch script before I submit it."
* "Submit it on standard with 8 cores for two hours." (you approve the plan first)

The assistant works as you: it sees your jobs and your files, nothing more, and every action is checked against limits Research Computing sets per person. There is no shell tool.

Other guides in this set: [Getting started](../rc-cluster-getting-started/), [Web portal](../rc-cluster-web-portal/), [Software](../rc-cluster-software/).

## Before you start

* A cluster account (see [Getting started](../rc-cluster-getting-started/)).
* Research Computing adds your `NETID@ucr.edu` account to the server's sign-in list and sends you its address. Below it appears as `SERVER_URL`.
* An AI assistant that supports remote MCP servers. You sign in through your browser with your UCR Google account; there is no API key to copy.

## Connect your assistant

Pick your client. Each one opens a browser for sign-in the first time.

**Claude Code**

```bash
claude mcp add --transport http rc-cluster SERVER_URL
```

Then in Claude Code run `/mcp`, choose `rc-cluster`, **Authenticate**, and sign in. Add `-s user` to the first command to use it in every project.

**Claude Desktop or claude.ai**: Settings, Connectors, **Add custom connector**, name `RC cluster`, URL `SERVER_URL`. On a Team or Enterprise plan an owner may need to allow custom connectors first.

**OpenAI Codex CLI** (0.160 or later)

```bash
codex mcp add rc-cluster --url SERVER_URL
```

**Gemini CLI**

```bash
gemini mcp add -s user -t http rc-cluster SERVER_URL
```

Start `gemini`, then run `/mcp auth rc-cluster`.

**OpenCode**

```bash
opencode mcp add rc-cluster --url SERVER_URL
opencode mcp auth rc-cluster
```

**Hermes Agent**

```bash
hermes mcp add rc-cluster --url SERVER_URL --auth oauth
hermes mcp test rc-cluster
```

Any other client that supports remote MCP servers over HTTP with OAuth sign-in works the same way: give it `SERVER_URL` and sign in when asked.

Check that it works: ask your assistant "What partitions does the RC cluster have?"

## What it can do

**Look things up** (no approval needed):

* Cluster status, partitions and their limits, and how busy each one is.
* Your jobs: list, details, efficiency, the end of a log, and a plain-language explanation of why a job failed or is still pending.
* Software: search modules, see what a module sets and what it needs loaded first, find containers, tested recipes and shared reference databases, and read a program's help text.
* Your files: list a folder or read a text file in your home or scratch. Hidden and credential-like files are never shown.
* Check a batch script against the cluster before you submit it, and test that modules load (in a short job on the `check` partition, never on the login node).
* Your own usage and avoidable waste, and the exact command for an interactive session.

**Act on your own jobs** (two steps, you approve each one):

* Submit, cancel, hold or release a job. Step one returns a plan: the checked script, partition, nodes, time limit, when Slurm expects it to start, and any problems. Nothing happens until step two confirms that exact plan. A plan expires after 10 minutes and works once.
* Upload an input file for a job, and get download links for its results.

Read the plan before you say yes. Assistants can be wrong; the plan is what will actually run.

## Limits

Each person has caps set by Research Computing: nodes and hours per job, and how much can be submitted per day. A plan that goes over a cap is refused with the reason. If a cap blocks real work, email us.

The server records each tool call (who, which tool, the command it ran, and the result size), not the conversation with your assistant. Job output and log text that comes back to the assistant is marked as untrusted data, so a file can't instruct the assistant to do something.

## Tips

* Give job IDs: "explain job 1234" is faster than "my job from this morning".
* For work that will take many hours or many nodes, have the assistant check the script and show the plan, then think about it before confirming.
* The assistant's view is your view: it can't see other people's jobs or files, and it can't change the cluster.

## Get help

Email [research-computing@ucr.edu](mailto:research-computing@ucr.edu) with your NetID, your client and what it said. If sign-in fails with an access message, you are probably not on the sign-in list yet.
