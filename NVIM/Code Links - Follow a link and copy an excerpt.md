# Follow a link and copy an excerpt

Our Code Links plugin works inside Neovim as well as Obsidian. It recognizes links in Markdown, plain text, and code comments. A link in a Python comment remains a comment; following it does not execute the Python program.

## How do I follow a link?

1. Open this note in Neovide, or open `scripts/link-practice.py` from this vault in Finder.
2. Press `Esc` and put the cursor anywhere inside the example link below. In the Python file, use the link in its first comment.
3. Press **Space, o, l**. The source file should open. You can also type `:CodeLinksFollow` and press Enter.

{{scripts/link-practice.py|Practice Python file}}

The target is relative to the vault/project root, not to the folder containing this note. The label after `|` changes the displayed text, not the destination. The Neovim plugin locates the root using `.code-links.json`, `.git`, or `.obsidian`.

## How do I read an excerpt and reach the real file?

Place the cursor in this link and press **Space, o, l**:

{{scripts/link-practice.py#L4-L6|The calculation}}

The selected lines open in a read-only excerpt tab. Its numbers refer to the source file. Move to a line and press **Enter** to open the editable source at that line. Press `q` while still in the excerpt to close the excerpt tab.

## How do I make a link to selected lines?

1. Open the real `scripts/link-practice.py` file and save it with `:w`.
2. Type `4G` to go to line 4. Press `V`, then `2j` to select lines 4 through 6.
3. Press **Space, o, t**. The plugin copies a tracked excerpt link to the system clipboard.
4. Paste it into an Obsidian note with Command-V, or into a Neovim buffer in Normal mode with `"+p`.

Keep the generated tracking suffix when you paste. It lets the reader relocate the excerpt when nearby lines move; if the source changes too much to match, the link needs review. It is not a guarantee that any arbitrary edit can be recovered.

## Where is this file referenced?

Open the source file and press **Space, o, b**, or run `:CodeLinksBacklinks`. References found in the current project appear in the quickfix list. Press Enter on a result to open it. This is a project search, not a search of every vault on your Mac.

## What if the file index is stale?

Run `:CodeLinksRefresh`. Refreshing helps discover files; it cannot repair an external file that moved to a different path. The managed rename command is **Space, o, r** / `:CodeLinksRename`; it previews affected references and asks before applying the rename. Save modified buffers first.

These shortcuts are from your installed {{code-links:nvim/lua/code_links/init.lua|Code Links implementation}}. The newer grouped links and double-backtick directory shorthand are documented for Obsidian; this tutorial uses the simpler syntax supported by the Neovim reader.
