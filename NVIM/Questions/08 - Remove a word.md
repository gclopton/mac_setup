---
study:
  version: 1
  id: nvim-first-08
  title: Remove a word
  sourceOrder: 8
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
        - dw
      caseSensitive: true
---

## Prompt

The cursor is on the r in `radius area volume`, in Normal mode. Use the delete operator followed by the next-word motion to remove `radius `, leaving `area volume`.

{{blank:keys}}

## Explanation

d is the delete operator and w moves to the next word. Together, dw deletes up to that next word beginning. This example starts at the word beginning and includes its following space.

[[NVIM/Neovim - Your first editing session#Practice reference|Review this command]]
