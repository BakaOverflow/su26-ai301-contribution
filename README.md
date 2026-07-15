# su26-ai301-contribution
Codepath AI301 Capstone for summer 26 open source project contributions

# Contribution [#1497]: [Fix several misleading/incorrect code comments across src/]

**Contribution Number:** 1  
<!--/] **Contribution Number:** [1 / 2 / 3]   -->
**Student:** George A

**Issue:** https://github.com/MFlowCode/MFC/issues/1497

**Branch link:** https://github.com/BakaOverflow/MFC/tree/fix-issue-1497

**Status:** Phase IV - Complete - (PR 1624 closed unmerged)
<!-- **Status:** [Phase I / Phase II / Phase III / Phase IV] [In Progress / Complete] -->
---

- [X] Phase I: Issue link + problem summary + why you chose this issue
- [X] Phase II: understanding the issue + reproduction process + solution approach
- [X] Phase III: testing strategy + implementation notes
- [X] Phase IV: PR link + summary + maintainer feedback log

**PR Link** [#1624](https://github.com/MFlowCode/MFC/pull/1624) **closed unmerged**

## Why I Chose This Issue

I found the issue interesting because it involves fixing basic mistakes in commented code while giving me the opportunity to dive into the contents of the codebase and determine whether I would like to tackle the more involved issues present. It matches my skill set because the codebase involves handling Python, CMake, and shell code. One of the learning goals I would like to achieve is leveraging AI to jump into programming languages I may not have much experience with, as the largest portion of this codebase is written in Fortran.

What I hope to learn are the basic challenges and expectations that come with contributing to open source code such as making sure I follow documented guidelines and etiquette correctly, and learning from any mistakes I might make along the way. I also hope to better use AI, specifically LLMs and possibly coding agents, to help accelerate learning and interacting with new languages, while also improving the quality of my open source contributions.


---

## Understanding the Issue

### Problem Description

From the issue linked above, the problem involves fixing misleading/incorrect code comments across multiple Fortran source files located in the src/ directory.

### Expected Behavior

Code comments in the Fortran source files located in the src/ directory should correctly state what the code statements/blocks perform, making it possible for programmers going through the code to understand the logic and operations involved.

### Current Behavior

Since these are code comment mistakes, there are no behavior changes that affect the code during runtime/compilation. Instead, the comments reflect incorrect information that doesn't represent what the code does, causing possible errors/misinterpretations.

### Affected Components

The following files are affected:
```
src/common/m_helper.fpp:524 and :536
src/simulation/m_collisions.fpp:7-8
src/simulation/m_data_output.fpp:48
src/common/m_derived_types.fpp:305-306
src/simulation/m_riemann_solver_hllc.fpp:140 (issue cited m_riemann_solvers.fpp:1779; file split upstream since)
src/common/m_precision_select.f90:16
src/common/m_mpi_common.fpp:42 and :429-430
```

One example is the m_helper file. There is a comment mistake in m_helper.fpp at lines 524 and 536, where we have the following:

```fortran
integer, parameter :: int64_kind = selected_int_kind(18)  !< 18 bytes for 64-bit integer
```

The comment "18 bytes..." is incorrect, as noted in the issue, because the 18 in ```selected_int_kind(18)``` specifies the number of decimal digits the integer can represent, not 18 bytes (a 64-bit integer is 8 bytes).

---

## Reproduction Process

### Environment Setup

Forked and cloned MFC. Verification was performed against `master` @ `0c772e60`
(full SHA `0c772e604e7d997b32720c303f628c18f5e0ea9f`); the issue was filed against
`40dde5e`, so I re-checked every reference before editing.

This is a comment-only change with no runtime behavior, so I verified statically against master rather than building MFC locally.

Working branch: https://github.com/BakaOverflow/MFC/tree/fix-issue-1497



### Steps to Reproduce

Because all seven items are comment-only (no runtime behavior), "reproduction"
means verifying each comment is wrong against the surrounding code at current master.
Anyone can follow these:


1. `git clone https://github.com/MFlowCode/MFC.git && cd MFC`
2. `git checkout 0c772e604e7d997b32720c303f628c18f5e0ea9f`
3. Open each location below and compare the comment to the code:
   - `src/common/m_helper.fpp:524` & `:536` — `selected_int_kind(18)` commented "18 bytes".
     `selected_int_kind(R)` requests a kind holding R *decimal digits*, not bytes
     (a 64-bit int is 8 bytes). Wrong on both lines.
   - `src/simulation/m_collisions.fpp:7-8` — `@brief` describes a ghost-node immersed
     boundary method, but the module's public API is `s_apply_collision_forces` with
     wall-overlap distances, spring stiffness, and damping. Docstring is copy-pasted.
   - `src/simulation/m_data_output.fpp:48` — `Rc_min !< Rc criterion maximum`, yet
     `Rc_min` is assigned via `minval(Rc_sf)` and compared in `if (Rc_min_glb < Rc_min)`.
     It tracks a minimum. (Issue said line 47; now 48.)
   - `src/common/m_derived_types.fpp:305-306` — `p0 !< Bubble size`, `m0 !< Bubble velocity`,
     copy-pasted from `R0/V0` directly above (303-304). `p0` is pressure, `m0` is mass.
     (Issue said 258-259.)
   - `src/simulation/m_riemann_solver_hllc.fpp:140` — `! 6-EQUATION MODEL WITH HLLC HLLC ...`
     duplicate "HLLC". NOTE: issue cited `m_riemann_solvers.fpp:1779`, but that module
     was since split into per-scheme files; the comment now lives here.
   - `src/common/m_precision_select.f90:16` — `half_precision = 2 !< selected_real_kind(3, 4)`.
     The value is hardcoded to 2 while sibling kinds are *computed* via `selected_real_kind`;
     the bare comment implies computation.
   - `src/common/m_mpi_common.fpp:42` — `! Allocating buff_send/recv and. Please note...`
     dangling "and." (Issue said 41.)
   - `src/common/m_mpi_common.fpp:429-430` — two redundant `!>` summary lines above
     `s_mpi_reduce_maxloc`. (Issue said 426-427.)
4. Result: all seven confirmed present and incorrect at current master.



### Reproduction Evidence


- **Commit showing reproduction:** N/A — comment-only; verified statically (steps above)
  rather than via a runtime repro.
- **My findings:** All 7 items still wrong on master. Line numbers drifted for items
  3, 4, 7, and item 5 moved files entirely (`m_riemann_solvers.fpp` → `m_riemann_solver_hllc.fpp`)
  because the Riemann module was split. I will edit at the *current* locations and note the
  stale reference in the PR.

---

## Solution Approach

### Analysis

Root cause for each is a stale or copy-pasted comment — `selected_int_kind`'s argument
misread as bytes; docstrings/field comments duplicated from neighboring modules/fields
(IBM module, R0/V0, R0); a min mislabeled as max; a hardcoded constant documented as if
computed; an editing artifact ("and."); and a duplicated summary/token. No code logic is
involved.

### Proposed Solution

Edit only the comment text at each location; touch no executable lines and add no
incidental whitespace/reformatting. The maintainer pre-specified wording for most items,
so I follow that to minimize review friction:

1. `m_helper.fpp:524, :536` → `!< kind holding >= 18 decimal digits (64-bit integer)`
2. `m_collisions.fpp:7-8` → rewrite `@brief` to match the module:
   `!> @brief Immersed-boundary collision handling: detects IB-IB and IB-wall contacts and applies soft-sphere collision forces and torques`
3. `m_data_output.fpp:48` → `!< Rc criterion minimum`
4. `m_derived_types.fpp:305-306` → `p0 !< Bubble pressure`, `m0 !< Bubble mass`
5. `m_riemann_solver_hllc.fpp:140` → remove duplicate: `! 6-EQUATION MODEL WITH HLLC star-state flux with contact wave speed s_S`
6. `m_precision_select.f90:16` → `!< hardcoded half-precision kind (approx. selected_real_kind(3, 4))`
7. `m_mpi_common.fpp:42` → `! Allocating buff_send/recv. Please note...`;
   `:429-430` → keep the precise `!>` line (429), delete the redundant one (430)

### Implementation Plan

**Using UMPIRE framework (adapted):**


- **Understand:** Seven independent comment errors across 7 files; comment-only, no behavior change.
- **Match:** Correct neighbors already exist — `R0/V0` (303-304) for item 4; computed
  `single/double_precision` (17-18) for item 6; the accurate `!>` line (429) for item 7b.
- **Plan:** One branch, one tidy commit, editing only the comment lines at the verified
  current-master locations above.
- **Implement:** branch `fix-issue-1497`; implemented in commit `72f617ad` (see Code Changes).
- **Review:** Self-reviewed against `.github/CONTRIBUTING.md`; ran `./mfc.sh format` and
  `./mfc.sh lint`; confirmed the diff is comment-only with no whitespace noise. PR will
  reference `Fixes #1497` and map each change to the issue's numbered list, noting the item-5 file move.
- **Evaluate:** Comments don't alter behavior, so the bar is: formatting + spell-check CI pass,
  the Doxygen docs build succeeds (relevant to the item-2 `@brief`), and the existing test
  suite stays green.

---

## Testing Strategy

These changes are comment-only and alter no executable code, so there is no runtime
behavior to unit- or integration-test and no regression test that could "catch the bug."
Validation was about confirming the diff is clean and the project's checks still pass.

### Validation performed
- `git diff` confirmed every change touches only comment text — no executable lines and
  no whitespace/reformatting noise (7 files, 10 insertions / 11 deletions, all comments).
- `./mfc.sh format` — 275 files left unchanged (no reformatting needed).
- `./mfc.sh lint` — ruff checks on toolchain/examples/benchmarks passed; 349 toolchain unit tests passed.
- Pre-commit precheck (same checks as the CI lint-gate) passed all 7 gates: formatting,
  spell check, toolchain lint, source lint, doc references, parameter docs, and example-case validation.
- Re-read each edited comment against the surrounding code to confirm it now matches behavior
  (e.g. `Rc_min` is assigned via `minval`; `p0`/`m0` are the bubble pressure/mass fields).

### Remaining CI at PR time
- The full simulation/build test suite runs on the PR; no behavior changed, so it should stay green.
- Doxygen docs build covers the rewritten `m_collisions` `@brief`.

---

## Implementation Notes

### Week 3 Progress
**What I did:** Applied all seven comment fixes on `fix-issue-1497` at the current-master
locations verified in Phase II, in a single atomic commit (`72f617ad`). Each edit changes only comment text:
- `m_helper.fpp` (524 & 536): "18 bytes" → "kind holding >= 18 decimal digits (64-bit integer)"
- `m_collisions.fpp` (7-8): replaced the copy-pasted IBM `@brief` with one describing the module's actual collision-force handling
- `m_data_output.fpp` (48): "Rc criterion maximum" → "minimum"
- `m_derived_types.fpp` (305-306): `p0`/`m0` "Bubble size/velocity" → "Bubble pressure/mass"
- `m_riemann_solver_hllc.fpp` (140): removed duplicate "HLLC"
- `m_precision_select.f90` (16): clarified `half_precision` is a hardcoded kind
- `m_mpi_common.fpp` (42): fixed dangling "and."; (429-430): removed the duplicate maxloc summary line

**Decisions:**
- One atomic commit for all seven (single issue, comment-only) to avoid flooding CI, per MFC's contributing guide.
- Followed the maintainer's pre-specified wording where given; authored the `m_collisions` `@brief` myself against the module's contained procedures (collision detection + soft-sphere force/torque application).
- Anchored the `p0`/`m0` edits on the variable names so the correct `R0`/`V0` "size/velocity" comments directly above were left untouched.

**Challenges:**
- The subtlest item was `selected_int_kind(18)` — its argument is a decimal-digit count, not a byte count, so the original "18 bytes" conflated the two (the 8-byte 64-bit kind holds ~18 digits).
- On my first commit I forgot to save `m_helper.fpp`, so it landed as a 6-file commit; caught it by comparing the diff's file count against the issue's seven items, then amended the commit to include it (now 7 files).

### Code Changes
- **Files modified:** `m_helper.fpp`, `m_collisions.fpp`, `m_data_output.fpp`, `m_derived_types.fpp`, `m_riemann_solver_hllc.fpp`, `m_precision_select.f90`, `m_mpi_common.fpp`
- **Key commit:** https://github.com/BakaOverflow/MFC/commit/72f617ad5ea644f50aeb8af80e9f452e45c6a4e8
- **Approach decisions:** comment-only diff, no executable or whitespace changes; verified via `git diff` and the pre-commit precheck.

---
## Pull Request

**PR Link:** https://github.com/MFlowCode/MFC/pull/1624

**PR Description:** Fixes all seven misleading/incorrect code comments from #1497 across seven `src/` files (comment-only, no behavior change), including the item that moved to `m_riemann_solver_hllc.fpp` after the Riemann module was split upstream.

**Maintainer Feedback:**
- [2026-06-24]: PR opened; requested review (`@claude full review`, tagged @sbryngelson). Awaiting review.

- [2026-07-07]: PR marked as draft, then closed unmerged by the maintainer with no comment. Issue #1497 remains open (the `Closes #1497` keyword only fires on merge).

**Status:** Closed (unmerged) Maintainer closed the PR without leaving a comment.

---

## Learnings & Reflections

### Technical Skills Gained
- The open-source contribution loop end to end: fork → branch → verify against upstream → commit → PR → review, using MFC's actual conventions (single atomic commit, `Closes #`, their PR template and `@claude` review bot).
- Reading an unfamiliar Fortran/CFD codebase well enough to confirm intent from surrounding code (e.g. that `Rc_min` is a minimum because it's assigned via `minval` and compared with `<`), despite no prior Fortran experience.
- A concrete Fortran detail: `selected_int_kind(n)` takes a count of decimal digits, not bytes — the bug at the heart of the `m_helper` fix.
- Git hygiene under review: syncing a fork, rebasing onto moving upstream, `--force-with-lease`, and keeping a diff scoped to comment-only lines.
- Running a project's local quality gates before pushing (`./mfc.sh format` / `lint`, the pre-commit precheck mirroring CI).

### Challenges Overcome
- The issue's file/line references were stale — filed against an older commit. Verifying against current `master` surfaced that three line numbers had drifted and item 5's whole file had been split (`m_riemann_solvers.fpp` → `m_riemann_solver_hllc.fpp`). Re-verified all seven and noted the move in the PR.
- Caught an incomplete commit by cross-checking the diff's file count (6) against the issue's item count (7) — I'd forgotten to save `m_helper.fpp`. Amended it in rather than shipping a partial fix.
- Separating "saved / committed / pushed" — more than once a change was on disk but not on the remote; learned to confirm with `git diff`/`git status` before assuming it landed.

### What I'd Do Differently Next Time
- Verify issue line references against current `master` up front, before planning, since the repo drifts.
- Treat "save → stage → commit → push" as one checklist and confirm the remote each time, rather than assuming an edit propagated.
- Sanity-check a commit's file count against the scope of the issue before pushing.

---

## Resources Used
- MFC contributing guide: https://mflowcode.github.io/documentation/contributing.html
- Issue #1497 (maintainer's pre-specified wording for most fixes): https://github.com/MFlowCode/MFC/issues/1497
- fortran-lang.org/learn (modern Fortran reference)
- Fortran `selected_int_kind` / `selected_real_kind` semantics (kind selection by digits/range)

---

# Contribution [#1485]: [Extend lint_source.py to catch stop/error stop, integer(wp), and signed d-exponent literals]

**Contribution Number:** 2  
**Student:** George A

**Issue:** https://github.com/MFlowCode/MFC/issues/1485

**Branch link:** https://github.com/BakaOverflow/MFC/tree/fix-issue-1485

**Status:** Phase II - Complete

- [X] Phase I: Issue link + problem summary + why you chose this issue
- [X] Phase II: understanding the issue + reproduction process + solution approach
- [ ] Phase III: testing strategy + implementation notes
- [ ] Phase IV: PR link + summary + maintainer feedback log

## Why I Chose This Issue

After my first contribution (#1497) was a comment-only documentation fix, I wanted a second issue that (a) builds on the MFC environment I already have set up and (b) is a step up in substance while still matching my strengths. #1485 is pure-Python work on `toolchain/mfc/lint_source.py`, MFC's CI source linter — squarely in my wheelhouse — and unlike my first issue it involves real logic and tests, not just comment text. It's also a CI/tooling contribution, which broadens my portfolio beyond documentation.

I hope to learn how a project encodes its own conventions as automated checks, get practice writing regex-based static analysis and its unit tests, and run a change through the same lint-gate my first PR passed through — this time contributing to that gate rather than just satisfying it.

---

## Understanding the Issue

### Problem Description

`toolchain/mfc/lint_source.py` is MFC's CI source linter: it scans `src/` Fortran (`.fpp` / `.f90`) line by line and flags text patterns that violate project conventions, failing the CI lint gate when it finds them. Three of MFC's conventions are currently enforced only by human code review, so violations keep slipping into the codebase. The issue asks to close those three gaps so the rules become CI-gated, scoped to these only:

1. **No `stop` / `error stop` check.** MFC wants `s_mpi_abort` instead (so all MPI ranks shut down cleanly); nothing currently flags a bare `stop`. Proposed `check_stop_statements`, exempting comments and Fypp `#:` lines, and allowlisting `syscheck/` plus the `#ifndef MFC_MPI` fallback in `m_mpi_common.fpp`.
2. **No `integer(wp)` check.** `wp` is a *real* (floating-point) kind, so `integer(wp)` is a copy-paste mistake that should be plain `integer`.
3. **The `d`-literal regex is too narrow.** `check_double_precision` matches only `[0-9]d0`, so signed/multi-digit double-precision exponents (`5.0d-11`, `2.5d+3`, `1.0d12`) are missed.

### Expected Behavior

Running `./mfc.sh lint` (or the pre-commit precheck / CI lint gate) should report an error for each of these convention violations present in `src/`, with a file:line reference and a suggested fix — the same way existing checks like `check_false_integers` and `check_raw_directives` already do.

### Current Behavior

The linter passes cleanly even though real violations exist in `src/` today. The rules are effectively unenforced: a contributor can add a `stop`, an `integer(wp)`, or a `5.0d-11` literal and CI will not object.

### Affected Components

```
toolchain/mfc/lint_source.py        # the linter to extend
toolchain/mfc/test_lint_source.py   # where the new tests go
```

Structure of the linter (verified against current `master`, not the issue's stale `40dde5e` base):
- `check_double_precision` (~line 182) — its regex still ends with the narrow `[0-9]d0` alternative (item 3 edits this).
- `check_false_integers` (~line 239) — flags bare `2_wp`; it does **not** cover `integer(wp)`, so item 2 is a genuine gap. This function is also the closest structural template for the new checks.
- `main()` (~lines 440–462) — aggregates every check via `all_errors.extend(check_...(repo_root))`. New checks must be registered here or CI will never run them.

---

## Reproduction Process

### Environment Setup

No new setup was needed: the MFC clone, Python venv, and toolchain from Contribution #1 were reused, and `./mfc.sh lint` / `./mfc.sh precheck` already run cleanly on this machine. Verified against `master` @ `db240d6` (the issue was filed against `40dde5e`, so — as with #1497 — I re-checked every reference before planning; line numbers had drifted again).

Working branch: https://github.com/BakaOverflow/MFC/tree/fix-issue-1485

### Steps to Reproduce

The bug here is a *silent gap*: the linter reports success while violations sit in the code. Each gap reproduces the same way — run the linter, then show the violation it failed to flag.

1. `git clone https://github.com/MFlowCode/MFC.git && cd MFC`
2. `git checkout db240d6` (or current `master`)
3. Run the source linter: `./mfc.sh lint`
   - **Observed:** passes — "Source lint passed", no errors reported.
4. **Gap 3 (d-literals):** `grep -n "5.0d-11" src/simulation/m_bubbles_EL.fpp`
   - Shows line 1346: `if ((1._wp - q_beta(1)%sf(i, j, k)) > 5.0d-11) then`
   - **Expected:** flagged as a double-precision literal. **Actual:** not flagged, because `check_double_precision`'s regex is `[0-9]d0`, which only matches a `d0` exponent — the signed `d-11` exponent slips through.
   - (Issue cited line 1345; it is 1346 on current master.)
5. **Gap 2 (`integer(wp)`):** `grep -rnE "\binteger\s*\(\s*wp\s*\)" src/`
   - Shows `src/simulation/m_bubbles_EE.fpp:73`: `integer(wp) :: i, j, k, l`
   - **Expected:** flagged, suggesting plain `integer`. **Actual:** not flagged — no such check exists.
6. **Gap 1 (`stop` / `error stop`):** `grep -rnE "^\s*(error\s+)?stop\b" src/ --include=*.fpp --include=*.f90`
   - Shows 7 matches. **Expected:** the non-allowlisted ones flagged, suggesting `s_mpi_abort`. **Actual:** not flagged — no such check exists.
7. Repeat steps 3–6: results are consistent, not a fluke.

### Reproduction Evidence

- **Branch:** https://github.com/BakaOverflow/MFC/tree/fix-issue-1485
- **Commit showing reproduction:** N/A — the reproduction is running the existing linter against unmodified `master`; no code change is needed to observe the gap.
- **My findings:** All three gaps confirmed on current `master`. I inventoried every violation the new checks will surface, since that directly determines this PR's scope:

| Gap | Real violations found | Locations |
|-----|----------------------|-----------|
| 3 — d-literal | 1 | `src/simulation/m_bubbles_EL.fpp:1346` (`5.0d-11`) |
| 2 — `integer(wp)` | 1 | `src/simulation/m_bubbles_EE.fpp:73` |
| 1 — `stop` | 3 (of 7 matches) | `src/pre_process/m_global_parameters.fpp:478`, `src/common/m_helper.fpp:105`, `src/post_process/m_global_parameters.fpp:409` |

The other 4 `stop` matches are the ones the issue explicitly allowlists: 2 in `src/syscheck/` and 2 in `src/common/m_mpi_common.fpp` (463, 465) — the non-MPI fallback path, where a plain `stop` is legitimate because `s_mpi_abort` is unavailable.

- **Important finding — the proposed regex produces false positives.** The issue's suggested pattern `[0-9]\.?[0-9]*[dD][-+]?[0-9]+` matches digit-`d`-digit sequences *inside identifiers*, not just literals. On current `master` it matches 4 lines, and 3 are false positives:

  ```
  src/post_process/m_start_up.fpp:49   :: cart2d12_coords, cart2d13_coords   <- "2d12" inside an identifier
  src/post_process/m_start_up.fpp:845  call MPI_CART_COORDS(..., cart2d12_coords, ierr)
  src/post_process/m_start_up.fpp:849  call MPI_CART_COORDS(..., cart2d13_coords, ierr)
  src/simulation/m_bubbles_EL.fpp:1346 ... > 5.0d-11) then                   <- the only real violation
  ```

  Shipped as specified, the check would flag three variable names as double-precision literals. The fix is to require the match not be part of an identifier — e.g. `(?<![A-Za-z0-9_])[0-9]\.?[0-9]*[dD][-+]?[0-9]+(?![A-Za-z0-9_])`. I will raise this in the PR rather than implementing the pattern as-written.

---

## Solution Approach

### Analysis

Root cause of each gap is simply an absent or under-specified rule in `lint_source.py`, not a defect in the Fortran:

- **Gaps 1 and 2** have no check function at all, so the conventions rely entirely on a reviewer noticing them.
- **Gap 3** has a check, but its regex (`[0-9]d0`) encodes only the narrowest form of the pattern it means to catch. Any exponent other than a literal `d0` — signed, or more than one digit — is invisible to it.

Secondary finding (above): the replacement regex proposed in the issue is too *broad* in the other direction, matching identifiers like `cart2d12_coords`. So the real task for item 3 is to widen the exponent handling **and** constrain the match to standalone literals.

### Proposed Solution

All work is confined to two Python files; no Fortran is modified (see scope note).

1. **`check_stop_statements`** (new) — walk `src/` `.fpp`/`.f90`, skip blank/comment lines and Fypp `#:` directives (so `#:stop` is exempt), match a `stop` / `error stop` statement on the code portion only, and allowlist `src/syscheck/` plus the non-MPI fallback in `m_mpi_common.fpp`. Message suggests `s_mpi_abort`.
2. **`integer(wp)` check** (new) — regex `\binteger\s*\(\s*wp\s*\)`; message suggests plain `integer`.
3. **Broaden `check_double_precision`** — replace the `[0-9]d0` alternative with an identifier-safe version of the issue's pattern (see finding above), case-insensitive, on the code portion only.
4. Register the new checks in `main()` alongside the existing `all_errors.extend(...)` calls.

**Scope note:** per the issue's own framing ("Closing the linter gaps makes them CI-gated… This pairs with the `stop`→`s_mpi_abort` and forbidden-literal cleanup issues"), I read this as **checks-only**, with the existing violations owned by #1483 (`stop`→`s_mpi_abort`) and #1484 (forbidden literals). The practical tension is that a check which fires on unfixed code turns this PR's own CI red, so I will raise it in the PR with the inventory above and let the maintainer choose: land the checks alongside #1483/#1484, or include the two one-line fixes (`m_bubbles_EE.fpp:73`, `m_bubbles_EL.fpp:1346`) here. I posted a claim comment on the issue on 2026-07-05; no maintainer reply as of this writing, so I am proceeding on the issue text rather than waiting.

### Implementation Plan

Using UMPIRE framework (adapted):

- **Understand:** Three convention rules are unenforced because `lint_source.py` either lacks a check or uses too narrow a regex. The linter should flag them at file:line with a suggested fix; today it passes silently while 5 real violations exist.
- **Match:** The codebase already contains the patterns I need. `check_false_integers` (~239) is a near-exact structural template for both new checks: compile a regex, walk the source files, skip comments, match the code portion, append `f"  {rel}:{i+1} <message>. Fix: <suggestion>"`. `check_double_precision` (~182) both hosts item 3 and demonstrates directory exclusion for the `syscheck/` allowlist. `main()` shows how checks are registered.
- **Plan:**
  1. Broaden the `d`-literal regex in `check_double_precision` (identifier-safe), and add a test proving `5.0d-11` is flagged and `cart2d12_coords` is not.
  2. Add the `integer(wp)` check modeled on `check_false_integers`; register in `main()`.
  3. Add `check_stop_statements` with the comment/`#:` skip and the `syscheck/` + `m_mpi_common.fpp` allowlist; register in `main()`.
  4. Run `./mfc.sh lint` to confirm the checks fire on exactly the inventoried violations and nothing else.
- **Implement:** branch `fix-issue-1485` (link above); commits to follow in Phase III.
- **Review:** Self-review against `.github/CONTRIBUTING.md`; run `./mfc.sh format` and the pre-commit precheck (the same seven gates as the CI lint gate) before pushing; keep the diff limited to the two toolchain files; PR will reference `Closes #1485` and document the false-positive finding.
- **Evaluate:** Unlike Contribution #1, this change has real testable behavior. For each of the three rules I will add a should-flag and a should-stay-clean case to `test_lint_source.py`, following the existing `..._is_flagged` / `..._is_clean` pattern (which uses `tmp_path` to write a small fake source file and assert on the returned errors). Success = new tests pass, the 349 existing toolchain tests stay green, and `./mfc.sh lint` reports exactly the inventoried violations with no false positives.

---

## Testing Strategy

[Phase III]

---

## Implementation Notes

[Phase III]

---

## Pull Request

[Phase IV]

---

## Learnings & Reflections

[To be completed]

---

## Resources Used

- Issue #1485: https://github.com/MFlowCode/MFC/issues/1485
- `toolchain/mfc/lint_source.py` (`check_double_precision`, `check_false_integers`, `main`) — structural templates
- `toolchain/mfc/test_lint_source.py` — existing test patterns to model new tests on
- MFC contributing guide: https://mflowcode.github.io/documentation/contributing.html
- Python `re` docs (lookbehind/lookahead for identifier-safe matching): https://docs.python.org/3/library/re.html




