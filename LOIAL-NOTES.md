# Loial Inc fork notes

This is the [Loial Inc](https://github.com/aditya-softwaroid) fork of
[mitre/stockpile](https://github.com/mitre/stockpile), maintained as
the canonical stockpile for [Crucible](https://github.com/aditya-softwaroid/Crucible)
v2.0+.

## Why a fork

[Crucible M30](https://github.com/aditya-softwaroid/Crucible/blob/main/RELEASE-NOTES-v2.0.md#dry-run-mode-m30)
adds a `dry_run` toggle on operations: when on, every link's command is
wrapped to print `[DRY-RUN] would execute: <command>` instead of running
the real command. This is safe for *most* abilities, but a few have
template-side side effects (e.g. `bash -c "$(curl ...)"`) where the
inner command runs at server-side template-expansion time, before the
agent ever sees the wrapper.

[Crucible M30.5](https://github.com/aditya-softwaroid/Crucible/blob/main/CrucibleVault/Frontend%20Rewrite/Milestones.md)
adds a per-ability `dry_run_safe: false` opt-out: planning_svc
*skips* the link entirely on dry-run instead of wrapping it. The
default is `true` (or unannotated, which falls back to a server-side
heuristic).

This fork's divergence from upstream: each ability that the heuristic
classifies as unsafe-for-dry-run gets an explicit `dry_run_safe: false`
field appended to its YAML. The annotation survives any future change
to the heuristic.

## How the annotation is maintained

Run `scripts/annotate_dry_run_safe.py` (lives in the Crucible main
repo, not here) against this fork's `data/abilities/` directory. The
script is idempotent and uses a text-append so unrelated lines aren't
reformatted. Re-run after pulling upstream changes.

## Upstream tracking

Default branch tracks `mitre/stockpile@master`. Re-base / merge
upstream periodically; re-run the annotation script after each rebase
to flag any new abilities that match the heuristic.

## Crucible compatibility

This fork is compatible with Crucible v2.0 and later. v1.0 and the
upstream MITRE Caldera don't read the `dry_run_safe` field — it's
silently ignored.
