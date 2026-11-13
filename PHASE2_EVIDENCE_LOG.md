# Phase 2 Evidence Log — GitHub Reverse Engineering Challenge
**Repository:** https://github.com/SkandaShandilya/REA-test  
**Local folder:** `C:\Users\skand\OneDrive\Desktop\REA-Zeph`  
**Author:** Skanda S Shandilya (`skandashandilya@gmail.com`)  
**Log generated:** 2026-11-13T21:37 IST

---

## E-001 — Repository Initialisation

| Field | Value |
|---|---|
| **Timestamp (IST)** | 2026-11-13 20:10:06 |
| **Timestamp (UTC)** | 2026-11-13T14:40:06Z |
| **What I Did** | Created the GitHub test repository `SkandaShandilya/REA-test` on GitHub and confirmed it was accessible. |
| **What I Observed** | GitHub API confirmed: `id: 1456712376`, `name: REA-test`, `full_name: SkandaShandilya/REA-test`, `default_branch: main`, `visibility: public`, `has_pull_requests: true`, `created_at: 2026-11-13T14:40:06Z`. |
| **Evidence classification** | **CONFIRMED** |
| **Evidence source** | GitHub REST API: `GET /repos/SkandaShandilya/REA-test` |
| **Limitations** | None. API response directly confirmed all repo properties. |

---

## E-002 — First Commits to `main`

| Field | Value |
|---|---|
| **Timestamp (IST)** | 2026-11-13 20:22:30 → 20:24:08 |
| **Timestamps (UTC)** | `aaced52` at 14:52:30Z; `c94fa16` at 14:54:08Z |
| **What I Did** | Created `notes.md` and committed it to `main` via GitHub web editor in two commits. |
| **What I Observed** | Commit `aaced52ef416fe9e7b1a061003a926417f4f408e` — "Create notes.md" — authored and committer-signed by GitHub (`noreply@github.com`), `verified: true`. Commit `c94fa16706b1c96c931af413bf1196ff244d1968` — "Add Test Observation Log section to notes" — same verification. Both confirmed by `git log --all` and GitHub API `/commits`. |
| **Evidence classification** | **CONFIRMED** |
| **Commit hashes** | `aaced52`, `c94fa16` |
| **Evidence source** | Local: `git log --all --graph --oneline`; Remote: `GET /repos/SkandaShandilya/REA-test/commits` |
| **Limitations** | Commits made via GitHub web UI — no local `git push` log for these two; confirmed via API instead. |

---

## E-003 — History Conflict Discovery (Orphan Branch Issue)

| Field | Value |
|---|---|
| **Timestamp (IST)** | 2026-11-13 20:43:10 |
| **What I Did** | Initialized local git repo in REA-Zeph, created `observation-log.md`, committed as `989904b`, renamed branch to `main`, checked out `feature-branch`, and pushed to `origin/feature-branch`, then `origin/feature-pr-test`. |
| **What I Observed** | Local commit `989904b` ("Add observation log for E-004 PR test") was an **orphan root commit** — no common ancestor with `origin/main` (`aaced52` → `c94fa16`). Two completely disconnected root commits confirmed in `git log --all --graph`. GitHub allowed the push but would block PR merge due to unrelated histories. |
| **Evidence classification** | **CONFIRMED** |
| **Commit hash** | `989904b` (orphan; still visible in local reflog and remote `feature-pr-test`) |
| **Evidence source** | `git reflog`, `git log --all --graph --oneline --decorate`, `git ls-remote origin` |
| **Limitations** | No GitHub PR was attempted against `main` from this orphan branch to capture the merge-conflict error message directly. The disconnect is structurally confirmed from the commit graph alone. |

---

## E-004 — Feature Branch PR Readiness

| Field | Value |
|---|---|
| **Timestamp (IST)** | 2026-11-13 21:30:06 (commit) / 21:30:09 (push) |
| **Timestamp (UTC — commit)** | 2026-11-13T16:00:06Z |
| **Timestamp (UTC — PR created)** | 2026-11-13T16:11:02Z |
| **What I Did** | Synced local `main` to `origin/main` (`git reset --hard origin/main`), created branch `feature-automation-test` off `main`, appended E-004 PR Validation section to `notes.md`, committed as `b2f0264`, pushed to `origin/feature-automation-test`, and opened Pull Request #1 targeting `main`. |
| **What I Observed** | Push succeeded: `* [new branch] feature-automation-test -> feature-automation-test`. PR #1 created with title "feat: update notes.md for PR validation (E-004)". GitHub API (`GET /repos/SkandaShandilya/REA-test/pulls/1`) confirmed: `state: open`, `merged: false`, `merged_at: null`, `closed_at: null`, `mergeable: true`, `mergeable_state: clean`, `additions: 3`, `deletions: 0`, `changed_files: 1`. Head branch: `feature-automation-test` (SHA `b2f0264`). Base branch: `main`. |
| **PR number** | `#1` |
| **PR title** | `feat: update notes.md for PR validation (E-004)` |
| **PR URL** | https://github.com/SkandaShandilya/REA-test/pull/1 |
| **PR status** | ✅ **Open** (not merged, not closed) |
| **Source branch** | `feature-automation-test` |
| **Target branch** | `main` |
| **PR changed files** | `notes.md` (+3 lines, 0 deletions) |
| **Merge conflicts** | None — `mergeable_state: clean` |
| **Evidence classification** | **CONFIRMED** |
| **Commit hash** | `b2f0264154b690496034ba4f1e4ceed422d17a16` |
| **Evidence source** | `git log`, `git push` output, `GET /repos/SkandaShandilya/REA-test/compare/main...feature-automation-test`, `GET /repos/SkandaShandilya/REA-test/pulls/1` |
| **Limitations** | None. PR is live and API-confirmed open. |

---

## E-005 — GitHub Actions CI Workflow

| Field | Value |
|---|---|
| **Timestamp (IST)** | 2026-11-13 21:31:37 (commit) / 21:31:40 (push) |
| **Timestamp (UTC — commit)** | 2026-11-13T16:01:37Z |
| **Timestamp (UTC — run created)** | 2026-11-13T16:01:45Z |
| **Timestamp (UTC — run completed)** | 2026-11-13T16:01:52Z |
| **What I Did** | Created `.github/workflows/ci.yml` with CI workflow triggered on push/pull_request to `main`. Committed as `aba284c` and pushed to `origin/main`. |
| **What I Observed** | GitHub Actions automatically triggered workflow run ID `43458514877` (run #1, workflow name "CI", workflow ID `417462500`). Event: `push`. Head branch: `main`. Head SHA: `aba284c`. Final status: `completed`. Conclusion: **`success`**. Total duration: ~7 seconds (started 16:01:48Z, completed 16:01:51Z). |
| **Job details** | Job: `ci-echo-test` (ID `131998921343`), runner: `ubuntu-latest`. Steps: Set up job ✅ → Checkout repository ✅ → Echo validation message ✅ → Post Checkout ✅ → Complete job ✅ |
| **Workflow run URL** | https://github.com/SkandaShandilya/REA-test/actions/runs/43458514877 |
| **Job URL** | https://github.com/SkandaShandilya/REA-test/actions/runs/43458514877/job/131998921343 |
| **Evidence classification** | **CONFIRMED** |
| **Commit hash** | `aba284cb487e83d7be5e4a5e411febd97f89a0e0` |
| **Workflow file** | `.github/workflows/ci.yml` on branch `main` |
| **Evidence source** | `GET /repos/SkandaShandilya/REA-test/actions/runs/43458514877` + `/jobs` |
| **Limitations** | Step log text for "Echo validation message" not downloaded (requires auth token for log download API); step name and conclusion are confirmed. |

---

## Git History Summary

```
* aba284c  (main, origin/main)          ci: add GitHub Actions workflow with echo test (E-005)
| * b2f0264  (feature-automation-test)  feat: update notes.md for PR validation (E-004)
|/
* c94fa16                               Add Test Observation Log section to notes      [E-002]
* aaced52                               Create notes.md                                [E-002]
* 989904b  (feature-pr-test)            Add observation log for E-004 PR test          [E-003 - orphan root]
```

---

## Files Created or Modified

| File | Action | Branch |
|---|---|---|
| `notes.md` | Created then modified | `main`, `feature-automation-test` |
| `observation-log.md` | Created (orphan root commit) | `feature-pr-test` |
| `.github/workflows/ci.yml` | Created | `main` |
| `PHASE2_EVIDENCE_LOG.md` | Created (this file) | local `main` |

---

## Status Summary

> [!NOTE]
> All E-001 to E-005 observations are fully confirmed. No manual steps remain.

| ID | Description | Status |
|---|---|---|
| E-001 | Repository initialised on GitHub | ✅ CONFIRMED |
| E-002 | First commits to `main` (`aaced52`, `c94fa16`) | ✅ CONFIRMED |
| E-003 | Orphan-branch history conflict discovered | ✅ CONFIRMED |
| E-004 | Feature branch created, committed, pushed, PR #1 opened | ✅ CONFIRMED |
| E-005 | GitHub Actions CI workflow triggered and passed | ✅ CONFIRMED |

> [!IMPORTANT]
> **Do NOT merge PR #1** at https://github.com/SkandaShandilya/REA-test/pull/1 — keep it open as evidence.
