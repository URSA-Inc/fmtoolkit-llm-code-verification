# fmtoolkit-llm-code-verification

Open source maintainers are absorbing a fast-growing volume of AI-generated pull
requests. Each one reads confidently and looks plausible whether or not it
actually preserves the code's behavior, and a test suite only checks the inputs
someone already thought to write down. Reviewing that volume by hand does not
scale, and behavioral regressions slip through.

This project gives participating open source projects free access to **FMToolkit**
to help manage that load. For a proposed change, FMToolkit checks whether the new
code behaves identically to the old code across **all** inputs — and when it does
not, it returns the exact input where they differ. That turns an unbounded review
problem into a triage list: changes proven to preserve behavior, and changes that
demonstrably alter it.

**You bring the commits; we verify them. We do not provide or run the model that
wrote the code** — projects use whatever tools their contributors already use.
What we offer is the verification: a way to tell, at scale, which AI-generated
changes are safe to merge.

## The value we're trying to give

- **Cut the review burden on a finite resource — maintainer attention.** A change
  proven behavior-preserving can be reviewed for style and intent rather than
  re-derived line by line.
- **Catch regressions that tests miss.** Equivalence checking reasons about every
  input at once, not just the sampled ones, so it surfaces the edge-case
  divergences AI-generated code tends to introduce.
- **Make the result auditable.** Every verdict is recorded against the change, so
  six months later it is clear what was checked and how strong the check was.

## How a project uses it

Two entry points, both pointed at code a project already has:

- **On pull requests (CI gate).** Run FMToolkit on the diff in a PR. Each modified
  function gets a verdict, posted back to the PR, so maintainers see which changes
  are proven safe before they review.
- **Across history (batch audit).** Walk a repository's existing commits and flag
  any that changed behavior without acknowledgement — useful for projects that
  have already merged a backlog of AI-generated contributions.

For each function in a change, FMToolkit returns one of:

- `equivalent` — the change provably preserves behavior on all inputs.
- `not_equivalent` — it changes behavior, with a concrete counterexample input.
- `inconclusive` — it could not be decided within the analysis budget.

Verdicts are graded by how completely the function could be modeled — a complete
proof, or a high-confidence result with small, stated gaps — and that grade is
recorded alongside the verdict so maintainers can set their own merge policy.

## Example

The companion write-up, *"Trust but Verify: Using a Neurosymbolic Model in Code
Optimization,"* shows the verification working against real, machine-generated
changes to the `maths/` tree of
[TheAlgorithms/Python](https://github.com/TheAlgorithms/Python). Across 214
in-scope functions, FMToolkit:

- confirmed **28** changes as behavior-preserving (14 as complete proofs), each of
  which also passed the existing tests;
- flagged **7** plausible-looking changes as behavior-altering, returning the
  triggering input for each — for example a floating-point divergence that only
  appears at `radius = 1000000.0`. None of the seven were caught by the project's
  existing tests.

That gap — changes that pass every test but still change behavior — is exactly the
risk that volume of AI-generated commits multiplies, and what this access is meant
to help with. (The write-up also documents one case the high-confidence tier got
wrong and how it was caught; we publish the misses too.)

## Getting access

Maintainers can request access by [opening an issue](../../issues/new) with a link
to the repository and where you'd like to start (PR gating, a history audit, or
both). Access is free for participating open source projects.

## What's in this repository

The integration and client tooling that connects a project's code to FMToolkit —
the CI check, the batch-audit command, result reporting, and configuration. The
verifier itself is the FMToolkit service, reached through a documented interface;
this repo does not contain it, and it does not contain or call a code-generating
model.

## Requirements

- A **git repository** and the changes you want checked (however they were
  produced).
- An **FMToolkit access token** (issued to participating projects).
- Python 3.11+ to run the client.

No model API key is required — FMToolkit verifies code, it does not generate it.

## Usage

> Commands are illustrative — adjust to the installed client.

```bash
# In CI, gate a pull request:
fmtoolkit verify-pr --base origin/main --head HEAD

# Audit already-merged history:
fmtoolkit audit-history --since v2.3.0
```

A GitHub Action wrapper for the PR gate is provided in `.github/`.

## Limitations

- **Not every function can be modeled.** Dynamic constructs and missing type
  annotations put some functions out of scope; those are reported as such rather
  than guessed at. In the pilot, just over half the functions were in scope.
- **`inconclusive` is a common outcome** and means exactly that — no claim either
  way. Such changes still need human review.
- **A high-confidence verdict is not a proof.** Its grade is recorded so it can be
  treated differently from a complete proof.
- **Equivalence is about behavior, not performance or style** — it tells you a
  change is safe, not that it is good.

## Contributing

Contributions are welcome under the project license (inbound = outbound). See
[CONTRIBUTING.md](CONTRIBUTING.md). Useful areas: CI integrations for more forges,
reporting formats, and configuration for project-specific merge policies.

## License

Licensed under the **Apache License 2.0** — see [LICENSE](LICENSE). Apache-2.0
includes an explicit patent grant and does not grant trademark rights.

## Trademark

"FMT" and "FMToolkit" are trademarks of URSA. The Apache-2.0 license
does not grant rights to use these marks. See [NOTICE](NOTICE).
