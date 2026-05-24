---
name: atomic-ops
description: Batch all related command, file, repo, Docker, artifact, and side-effecting work into one ephemeral Python script with fail-fast execution, exit-code capture, independent verification, and explicit OK/FAIL reporting.
---

{
  "purpose": "Reduce tool-call chatter, prevent hidden partial state, and make every operation batch fail-fast, reproducible, and self-verifying.",
  "contract": {
    "atomic_boundary": "whole_batch",
    "rules": [
      "Use one ephemeral Python script for the full bounded batch.",
      "Batch discovery, execution, verification, and reporting together.",
      "Discover current state before mutation.",
      "Execute planned operations in order.",
      "Capture exit codes, stdout, stderr, and timeouts.",
      "Verify every expected outcome independently.",
      "Stop immediately on first failure.",
      "Print final OK only after the whole batch verifies."
    ]
  },
  "use_when": [
    "shell commands",
    "file reads",
    "file writes",
    "file patches",
    "repo inspection",
    "repo mutation",
    "Docker actions",
    "Docker Compose actions",
    "Git operations",
    "installs",
    "builds",
    "tests",
    "artifact generation",
    "multi-step verification",
    "any side-effecting operation"
  ],
  "batch_rules": [
    "One script per bounded operation batch.",
    "Batch all related tool-call work into that script.",
    "Do not split discover, execute, verify, and report into separate tool calls when one script can do all.",
    "Later steps run only after earlier checks pass.",
    "Final report summarizes verified outcomes.",
    "Split batches only when human approval is required.",
    "Split batches only when scope is unclear.",
    "Split batches only when output is too large for one safe batch.",
    "Split batches only when a separate required tool cannot be called inside Python.",
    "Split batches only when next step depends on a verified result from the previous batch."
  ],
  "safety_rules": [
    "Never assume paths exist.",
    "Never assume files exist.",
    "Never assume branches exist.",
    "Never assume containers exist.",
    "Never assume services exist.",
    "Never assume commands exist.",
    "Never assume generated outputs exist.",
    "Discover before acting.",
    "Prefer exact targets over broad globs.",
    "Avoid destructive actions unless explicitly requested.",
    "Refuse ambiguous patches or multi-match edits.",
    "Use timeouts for subprocesses.",
    "Capture stdout and stderr.",
    "Print useful stdout and stderr.",
    "Print FAIL to stderr before any non-zero exit.",
    "Print final OK only after verification.",
    "Read back files after writing.",
    "Re-query external state after mutation.",
    "Check diffs after repo edits.",
    "Check build and test exit codes after validation commands.",
    "Do not continue after warning-level uncertainty that affects correctness."
  ],
  "avoid": [
    "raw shell for mutation when Python can wrap it",
    "separate tool calls for discover/edit/verify when one batch can do all",
    "intent-only success messages",
    "continuing after warnings that affect correctness",
    "ambiguous patches",
    "broad destructive actions unless explicitly requested",
    "unverified mutations",
    "assuming success from command output alone"
  ],
  "output_rules": {
    "success": "OK: <verified outcome>",
    "failure": "FAIL: <specific reason>",
    "batch_success": "OK: batch verified: <summary>",
    "batch_failure": "FAIL: <step> failed: <specific reason>"
  },
  "stop_conditions": [
    "non-zero exit",
    "timeout",
    "missing target",
    "ambiguous match",
    "unexpected diff",
    "unknown path",
    "partial failure",
    "unverified mutation",
    "warning that affects correctness"
  ],
  "done_criteria": [
    "All related operations ran inside one script.",
    "Every command exit code was checked.",
    "Every expected side effect was independently verified.",
    "Failures exited non-zero.",
    "Final output includes clear OK summary.",
    "No ambiguous or unverified state remains."
  ]
}