---
study:
  version: 1
  id: nvim-first-12
  title: Follow a source reference
  sourceOrder: 12
  type: blank
  concepts:
    - code-links
  courseIds:
    - tools-neovim
  chapters:
    - courseId: tools-neovim
      chapterId: first-steps
      title: First editing session
  answers:
    - id: keys
      accepted:
        - Space o l
        - space o l
        - <Space> o l
      caseSensitive: true
---

## Prompt

You are in Normal mode with the cursor inside a Code Links reference in a comment. Write our three-key follow-link sequence as words separated by spaces.

{{blank:keys}}

## Explanation

Space, o, l follows the link. The keys are pressed in sequence. :CodeLinksFollow is the equivalent command.

[[NVIM/Code Links - Follow a link and copy an excerpt#How do I follow a link?|Review this command]]
