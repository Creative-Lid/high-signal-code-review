# High-Signal Code Review

An agent skill that reviews code changes and returns only concise comments for proven defects. It filters out style preferences, speculative risks, harmless duplication, and generic test suggestions so the result reads like a useful human review rather than an audit dump.

## Install

```bash
npx skills add Creative-Lid/high-signal-code-review
```

Add `-g` to install it globally, or use the interactive installer to choose an agent and scope.

## Use

Invoke the skill without additional instructions to review the checked-out branch against its merge target:

```text
/high-signal-code-review
```

- The default scope compares committed changes on the checked-out branch with the merge target, usually the repository's default branch.
- Staged, unstaged, and untracked files are excluded from the default branch review, even when the working tree is dirty.
- Ask for `staged`, `uncommitted`, `local`, or `pre-commit` review when that is the intended scope.
- Repository instruction files such as `AGENTS.md` are context, not review targets, unless explicitly named.
- Any explicit commit, range, pull request, scope, or target overrides inference.

Before analyzing the diff, the skill reports the resolved review mode and exact snapshots. A branch review includes the head, target, merge base, and exclusion of local changes; self-review identifies the `HEAD`, index, and working-tree scope. It then reads and cites files from those snapshots, so unrelated edits cannot shift line numbers or change the code being reviewed.

This makes PR review the default while keeping self-review explicit and convenient:

- `/high-signal-code-review` reviews the checked-out PR or feature branch.
- `/high-signal-code-review local` reviews your staged, unstaged, and relevant untracked work.
- `/high-signal-code-review staged` reviews only what is currently in the index.

Examples:

```text
/high-signal-code-review staged
/high-signal-code-review uncommitted
/high-signal-code-review against main
/high-signal-code-review compare to development
```

Agents that use `$skill-name` invocation can run `$high-signal-code-review` with the same optional arguments.

## Output

The skill returns a verdict followed by short `path:line` comments only when a changed line causes a demonstrated wrong outcome. If no finding survives that standard, it approves the change without filler.

```markdown
**Verdict**: APPROVED WITH WARNINGS

**`src/orders/save-order.ts:40`** — `clearDraft()` also removes the receipt key that confirmation still reads. Clear only the draft key here and leave receipt cleanup to `sendReceipt()` after delivery succeeds.
```

Ask `expand 40` or `expand all` to get more detail about comments already reported without widening the review.

## License

MIT
