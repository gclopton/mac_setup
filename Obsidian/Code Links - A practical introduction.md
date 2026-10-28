# Code Links in Obsidian

The braces identify a source link, the path identifies the file, and the text after `|` supplies a readable label. Here is the source syntax:

```text
{{scripts/link-practice.py|Practice code}}
```

Here is the working link: {{scripts/link-practice.py|Practice code}}.

Click to open it; right-click for source navigation, separate previews, and the containing folder. The default launcher may reuse an existing Neovim session. Choosing a Finder default application does not itself change this plugin's launcher.

## How do I show only the relevant code?

Prefix the link with `!` and specify the source lines:

```text
!{{scripts/link-practice.py#L4-L6|Calculation}}{height=6}
```

!{{scripts/link-practice.py#L4-L6|Calculation}}{height=6}

Multiple ranges use commas, such as `#L4-L6,L9-L10`. Right-click the preview to reach the editable source. The height controls the preview's scrollable area; it does not change the file.

## How do I create a new script?

Type `{{new-example.py}}` in a note. If no matching file exists at the vault root or in `scripts`, the link is yellow in dark mode or dark gold in light mode. Clicking creates `scripts/new-example.py` and opens it. Right-click instead to choose another folder through the browser. Reading or hovering over the link does not create a file.

## How do I link to a folder?

Use `{{dir:scripts|Scripts folder}}`. The orange link opens the folder in Neovim's explorer; right-click opens Finder. This form also works with shared aliases. Double-backtick inline code explicitly prefixed with `dir:` is an alternative; ordinary inline code keeps its usual meaning.

{{dir:scripts|Scripts folder}}

## How do I combine references into one link?

Select existing code, note, PDF, or web links in the editor. Right-click and choose **Combine selected links…**, then enter a label. Use **Add from gallery…** to browse and preview additional files before saving. Click the resulting red label to reveal its references; right-click to edit or ungroup it.

{{links:Practice references|{{scripts/link-practice.py|Code}}; [[NVIM/Code Links - Follow a link and copy an excerpt|Neovim tutorial]]}}

## Where is the full reference?

Open **Code Links: Syntax and actions** from Obsidian's command palette. That guide covers aliases, source jumps, regular-expression excerpts, Wolfram files, reference-group limits, and slide behavior. It reads the maintained [Code Links guide](file:///Users/gradyclopton/Projects/code-links/docs/Using%20Code%20Links.md) rather than a separate frozen copy.

Use [[NVIM/Code Links - Follow a link and copy an excerpt|the Neovim tutorial]] for the keyboard commands inside the editor.
