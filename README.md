# su26-ai301-contribution
Codepath AI301 Capstone for summer 26 open source project contributions

# Contribution [#1497]: [Fix several misleading/incorrect code comments across src/]

**Contribution Number:** 1  
<!--/] **Contribution Number:** [1 / 2 / 3]   -->
**Student:** George A

**Issue:** https://github.com/MFlowCode/MFC/issues/1497

**Branch link:** https://github.com/BakaOverflow/MFC/tree/fix-issue-1497

**Status:** Phase II - Complete
<!-- **Status:** [Phase I / Phase II / Phase III / Phase IV] [In Progress / Complete] -->
---

- [X] Phase I: Issue link + problem summary + why you chose this issue
- [X] Phase II: understanding the issue + reproduction process + solution approach
- [ ] Phase III: testing strategy + implementation notes
- [ ] Phase IV: PR link + summary + maintainer feedback log

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
2. `m_collisions.fpp:7-8` → rewrite `@brief` to match the module, e.g.
   `!> @brief Immersed-boundary collision forces: wall-overlap distances and spring/damping
   normal & tangential forces and torques` (final `@brief` wording to be set in Phase III
   against the module's actual public procedures)
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
- **Plan:** One branch, one commit per logical group (or one tidy commit), editing only the
  comment lines at the verified current-master locations above.
- **Implement:** branch `fix-issue-1497` (link in Reproduction Evidence); commits added in Phase III.
- **Review:** Self-review against `.github/CONTRIBUTING.md`; run `./mfc.sh format`; confirm
  diff is comment-only with no whitespace noise; PR references `Fixes #1497` and maps each
  change to the issue's numbered list, noting the item-5 file move.
- **Evaluate:** Comments don't alter behavior, so the bar is: formatting + spell-check CI pass,
  the Doxygen docs build succeeds (relevant to the item-2 `@brief`), and the existing test
  suite stays green.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
