# EnvBranch — Consistent branching across a container and a database

Research memo written for the Columbia **DAPLab** student-researcher mini-task
*"EnvBranch: Research Ideas for Branchable Agent Environments"* (2026 Fall).

**→ [`envbranch-memo.pdf`](./envbranch-memo.pdf)** — 2 pages, including figure and references.

**Chosen direction:** coordinating branches across components such as a container and a database.

## Summary

Once an agent's state spans a Waypoint-managed container *and* an external database, "branch" stops
being a snapshot problem and becomes a **consistent-cut** problem. A container checkpoint captures the
application's *belief* about the database — the ORM identity map, pooled connections, an open
transaction on a socket fd — while the authoritative state sits on the far side of that descriptor,
together with session state (prepared statements, temp tables, session GUCs, advisory locks) whose
descriptor CRIU can restore but whose *peer* it cannot.

Two independently timed snapshots therefore leave **orphan** or **duplicate** commits at the boundary:
not data loss, but silent divergence, which a branching agent will treat as ground truth.

The memo's argument is that, unlike in Chandy–Lamport, the two components are **not peers**. The
container can only be checkpointed at *now*; the database is already versioned and can branch at an
arbitrary past point. So no marker protocol is needed — checkpoint the container at now, then
*retroactively* branch the database at the last commit the container observed. The memo sketches the
smallest mechanism that does this (a commit-witness shim plus a branch manifest), states a deliberately
**one-sided** guarantee, and proposes one falsifiable experiment with a stated kill criterion.

## Papers read

| | |
|---|---|
| Chandy & Lamport, *Distributed Snapshots* (ACM TOCS 1985) | [PDF](https://lamport.azurewebsites.net/pubs/chandy.pdf) — *found independently* |
| Ang et al., *BranchBench* | [arXiv:2604.17180](https://arxiv.org/abs/2604.17180) — *found independently* |
| Dong et al., *DeltaBox* | [arXiv:2605.22781](https://arxiv.org/abs/2605.22781) |
| Wang & Zheng, *Fork, Explore, Commit* | [arXiv:2602.08199](https://arxiv.org/abs/2602.08199) |
| Holmes et al., *Spice* (OSDI 2026) | [USENIX](https://www.usenix.org/conference/osdi26/presentation/holmes) |
| Xu, Zhou, Wu & Kaffes, *Toward Systems Foundations for Agentic Exploration* | [arXiv:2510.05556](https://arxiv.org/abs/2510.05556) |

## Repository layout

```
envbranch-memo.pdf     the submitted memo
src/memo.html          typesetting source — print to PDF from a browser to regenerate
src/memo.md            plain-text source for editing
```

## Author

Songhan (Mason) Wu · University of Michigan EECS, BS 2026 · Columbia MS in Artificial Intelligence, Fall 2026
