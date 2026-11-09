# High-Signal Code Review

An agent skill that reviews code changes and returns only concise comments for proven defects. It filters out style preferences, speculative risks, harmless duplication, and generic test suggestions so the result reads like a useful human review rather than an audit dump.

## Install

```bash
npx skills add Creative-Lid/high-signal-code-review
```

Add `-g` to install it globally, or use the interactive installer to choose an agent and scope.

## Use

Invoke the skill without additional instructions and it will infer the review scope from Git state:

```text
/high-signal-code-review
```

- A dirty working tree reviews staged, unstaged, and relevant untracked changes.
- A clean working tree compares the current branch with the repository's default target branch.
- An explicit scope or target overrides inference.

Examples:

```text
/high-signal-code-review staged
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
