---
study:
  version: 1
  id: nvim-first-09
  title: Remove the current line
  sourceOrder: 9
  type: blank
  concepts:
    - editing
  courseIds:
    - tools-neovim
  chapters:
    - courseId: tools-neovim
      chapterId: first-steps
      title: First editing session
  answers:
    - id: keys
      accepted:
        - dd
      caseSensitive: true
---

## Prompt

In Normal mode, which repeated two-letter command deletes the current line?

{{blank:keys}}

## Explanation

dd deletes a whole line and stores it in a register, so it can be put back with p.

[[NVIM/Neovim - Your first editing session#Practice reference|Review this command]]
