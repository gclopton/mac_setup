---
study:
  version: 1
  id: nvim-first-04
  title: Move to the next word
  sourceOrder: 4
  type: blank
  concepts:
    - movement
  courseIds:
    - tools-neovim
  chapters:
    - courseId: tools-neovim
      chapterId: first-steps
      title: First editing session
  answers:
    - id: keys
      accepted:
        - w
      caseSensitive: true
---

## Prompt

In Normal mode, the cursor is on the r in `radius area volume`. Which single lowercase key moves to the beginning of area?

{{blank:keys}}

## Explanation

w moves to the next word beginning. b moves back to a word beginning.

[[NVIM/Neovim - Your first editing session#Practice reference|Review this command]]
