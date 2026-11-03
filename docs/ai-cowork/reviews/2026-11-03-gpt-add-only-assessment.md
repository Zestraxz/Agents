# Add-only three-agent collaboration: initial repository assessment

Date: 2026-11-03
Repository: Zestraxz/Agents
Status: proposal and source-inspected findings only; no runtime test execution in this review.

## Absolute owner rule

GPT, Claude and Antigravity MUST NOT edit, overwrite, rename, delete, revert or rewrite any existing tracked artifact, including via formatters, generators, refactors, migrations, auto-fixes or release workflows. They MAY create new uniquely named files only. No force-push, history rewrite, direct mutation of main, or automatic merge. Existing repository guidance which requests editing existing status, changelog, coverage or configuration files is superseded for this initiative. Where the rule conflicts with established operations, log a proposal instead of silently changing behavior.

## Verified initial context

This repository is an upstream-style Microsoft 365 Agents SDK resource mirror/fork, with extensive samples and generated client code. Its README describes Microsoft SDK contribution processes, not a CH-specific multi-agent orchestration implementation. Adding orchestration logic into vendor trees would unnecessarily couple local policy to upstream code.

## Improvements to pursue (not yet implemented)

1. Keep Microsoft code, generated SDK models, sample resources, licenses and contributor conventions unchanged.
2. Treat this repository as a reference source for communication-channel design, not the authoritative shared cowork state.
3. If a local cowork proof of concept is needed, create a new isolated CH-specific example directory and document exact API and license compatibility.
4. Never claim end-to-end GPT/Claude/Antigravity SDK interoperability without a working integration test.

## GitHub-mediated handshake, common to all three agents

1. **DISCOVER**: read repository README, AGENTS, operational and security rules, existing artifacts, and current main commit SHA. Search for previous work and known rejections before proposing changes.
2. **PROPOSE**: create a new task file at `docs/ai-cowork/events/<UTC timestamp>-<agent>-<taskid>-propose.json` with task_id, agent, source_base_sha, source_paths, desired_output_paths, acceptance_tests, risk, and status.
3. **ACK**: the next agent creates a *different* `...-ack.json`, referencing the immutable proposal's path/hash. Never update the original event.
4. **CLAIM**: one implementer per task; use an agent-specific branch (for example `claude/add-only/<taskid>`), and either an isolated clone/worktree or strict single-tool local switch. Simultaneous writes in the same folder are prohibited.
5. **DELIVER**: produce only unique new paths (for replacements, use a new versioned name under a new namespace). Create a completion event with paths, test evidence, original sources, known limitations, and next reviewer.
6. **REVIEW**: a separate agent validates factual claims, tests, privacy, and potential regressions. Publish an immutable verdict event (approve/reject/needs-owner-action). An independent reviewer must not claim tests were executed when merely reviewing text.
7. **MERGE**: open a draft PR; only a human-approved or explicitly authorized maintainer merges. Recheck against the current merge base after rebasing or any upstream change. No automatic merging when source base has advanced.

### Required event fields

```json
{
  "protocol_version": "1.0",
  "task_id": "TASK-YYYYMMDD-UNIQUE",
  "event_id": "unique-per-event",
  "event_type": "PROPOSE|ACK|CLAIM|DELIVER|REVIEW|ACCEPT|REJECT",
  "agent": "gpt|claude|antigravity",
  "created_at_utc": "ISO-8601",
  "repo": "Zestraxz/Agents",
  "base_commit_sha": "40-character commit SHA",
  "predecessor_event_path": null,
  "source_paths": [],
  "created_paths": [],
  "test_evidence": [],
  "findings": [],
  "decision": "pending"
}
```

Every event is a new file. Event ordering derives from referenced predecessor paths and immutable Git commits, not clocks alone. If two agents claim the same task, both stop and request arbitration; neither may overwrite the competing claim. Credentials, access tokens, PII, and financial records must never appear in events or commits.

## Mandatory add-only validation

On every proposed PR, compare its full diff to the latest target branch: EVERY file status MUST be `A` (added), with no `M`, `D`, `R`, `C`, or `T`. Require that each path was absent from target HEAD, is under an approved new namespace, and has not been claimed by another active task. GitHub's create-file operation should fail when the destination already exists; never fall back to update-file. A path added in an earlier PR becomes immutable after merging.

Recommended read-only verification: `git diff --name-status origin/main...HEAD` plus `git diff --diff-filter=MDRCT --exit-code origin/main...HEAD`. Caution: validating only this diff is insufficient when another branch advances; revalidate at merge time. No test should write into tracked files.

## Repository-specific validation

npm run lint for changed JS if any; confirm zero modifications to vendored and generated paths

## Review integrity and next handoff

This is a **first-pass architecture and repository-content review**, not a comprehensive audit, test pass or security certification. Claims about runtime safety, production deployment, or quality scores remain unverified until independently tested. Next agent: read the original sources, confirm findings, choose the highest-risk actionable item, and create a NEW event and NEW proposed artifact. Do not touch this document after merge.
