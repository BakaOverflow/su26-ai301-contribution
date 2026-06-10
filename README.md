# su26-ai301-contribution
Codepath AI301 Capstone for summer 26 open source project contributions

# Contribution [#1497]: [Fix several misleading/incorrect code comments across src/]

**Contribution Number:** 1  
<!--/] **Contribution Number:** [1 / 2 / 3]   -->
**Student:** George A

**Issue:** https://github.com/MFlowCode/MFC/issues/1497

**Status:** Phase I
<!-- **Status:** [Phase I / Phase II / Phase III / Phase IV] [In Progress / Complete] -->
---

- [X] Phase I: Issue link + problem summary + why you chose this issue
- [ ] Phase II: understanding the issue + reproduction process + solution approach
- [ ] Phase III: testing strategy + implementation notes
- [ ] Phase IV: PR link + summary + maintainer feedback log

## Why I Chose This Issue

**[1-2 paragraphs explaining why this issue interests you, how it matches your skills/learning goals, what you hope to learn]**

I found the issue interesting because it involves fixing basic mistakes in commented code while giving me the opportunity to dive into the contents of the codebase and determine whether I would like to tackle the more involved issues present. It matches my skill set because the codebase involves handling Python, CMake, and shell code. One of the learning goals I would like to achieve is leveraging AI to jump into programming languages I may not have much experience with, as the largest portion of this codebase is written in Fortran.

What I hope to learn are the basic challenges and expectations that come with contributing to open source code such as making sure I follow documented guidelines and etiquette correctly, and learning from any mistakes I might make along the way. I also hope to better use AI, specifically LLMs and possibly coding agents, to help accelerate learning and interacting with new languages, while also improving the quality of my open source contributions.


---

## Understanding the Issue

### Problem Description

**[In your own words, what's broken or missing?]**

From the issue linked above, the problem involves fixing misleading/incorrect code comments across multiple Fortran Preprocessor files located in the src/ directory.

### Expected Behavior

**[What should happen?]**

Code comments in the Fortran Preprocessor files located in the src/ directory should correctly state what the code statements/blocks perform, making it possible for programmers going through the code to understand the logic and operations involved.

### Current Behavior

**[What actually happens?]**

Since these are code comment mistakes, there are no behavior changes that affect the code during runtime/compilation. Instead, the comments reflect incorrect information that doesn't represent what the code does, causing possible errors/misinterpretations.

### Affected Components

**[Which parts of the codebase are involved?]**

The following files are affected:
```
src/common/m_helper.fpp:524 and :536
src/simulation/m_collisions.fpp:7-8
src/simulation/m_data_output.fpp:47
src/common/m_derived_types.fpp:258-259
src/simulation/m_riemann_solvers.fpp:1779
src/common/m_precision_select.f90:16
src/common/m_mpi_common.fpp:41 and :426-427
```

One example is the m_helper file. There is a comment mistake in m_helper.fpp at lines 524 and 536, where we have the following:

```fourtran
integer, parameter :: int64_kind = selected_int_kind(18)  !< 18 bytes for 64-bit integer
```

The comment "18 bytes..." is incorrect, as noted in the issue, because the 18 in ```selected_int_kind(18)``` specifies the number of decimal digits the integer can represent, not 18 bytes (a 64-bit integer is 8 bytes).

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

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
