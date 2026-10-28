---
study:
  version: 1
  id: nvim-first-10
  title: Undo an edit
  sourceOrder: 10
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
        - u
      caseSensitive: true
---

## Prompt

In Normal mode, which single lowercase key undoes the most recent edit?

{{blank:keys}}

## Explanation

u undoes; Ctrl-r redoes. Uppercase U has different behavior.

[[NVIM/Neovim - Your first editing session#Practice reference|Review this command]]
