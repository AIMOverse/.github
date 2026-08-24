# Reusable workflows

Shared CI policy for the BitRouter harness plugins — [bitrouter-dsh][dsh],
[bitrouter-pi][pi], [bitrouter-opencode][oc].

Those three are separate repositories on purpose: three harnesses, three
package ecosystems, three release cadences, and a fork of any one of them
should be a complete thing. So almost everything about them stays local. What
lives here is only the part where three copies would be three *different*
behaviours rather than three identical ones.

| workflow | what it owns |
|---|---|
| `plugin-compat.yml` | what an agent may change when an upstream moves, when it must decline, and what it must never touch |
| `plugin-automerge.yml` | which pull requests are allowed to merge themselves |

Both are `workflow_call` only. The callers live in each plugin repository and
pass that repository's particulars — install and test commands, which upstream
moved, which pull request to repair.

## Why these two and not the CI

A plugin's own build and test workflow is genuinely per-repo and gains nothing
from being shared: it is a handful of lines, it changes when that package
changes, and reading it should not require opening another repository. Policy
is the opposite. "May an agent rewrite a test to make its own fix pass" has one
right answer across all three, and the moment it has three copies it has three
answers.

## The safety model, briefly

Auto-merge is only safe because of two things it does not control:

- **Branch protection with required checks.** GitHub merges an auto-merge pull
  request as soon as nothing is outstanding. If nothing is required, nothing is
  outstanding, and auto-merge means merge-on-open.
- **A publish workflow that fires on a release, never on a push to `main`.**
  That separation is what lets `main` move on its own — it reaches nobody until
  a person tags it.

Neither is configured here. Both are per-repository, and both must be in place
before a caller is added.

[dsh]: https://github.com/bitrouter/bitrouter-dsh
[pi]: https://github.com/bitrouter/bitrouter-pi
[oc]: https://github.com/bitrouter/bitrouter-opencode
