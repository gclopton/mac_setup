# Code files and iTerm folders

## How do I open a code file?

Double-click a common text or source-code file in Finder. On Little brother, Neovide is now the default for common formats including `.txt`, `.py`, `.c`, `.cpp`, `.h`, `.lua`, `.js`, and `.json`. Neovide runs your existing Neovim configuration. A Python file was opened through Finder and visually checked on 2026-10-07.

## How do I open a folder in iTerm?

Select the folder in Finder and press **Control–Option–Command–T**. This is a Finder application shortcut for **New iTerm2 Tab Here**. The same action is available by right-clicking the folder and choosing **Services → New iTerm2 Tab Here**.

The shortcut assignment was saved and checked on 2026-10-07. Opening the resulting terminal was not tested by computer control because that tool blocks iTerm. If the shortcut is not immediately picked up by Finder, use the existing Services menu action and check System Settings → Keyboard → Keyboard Shortcuts → App Shortcuts → Finder.

## Where are these settings?

Neovim's configuration lives in `~/.config/nvim`. Finder's application shortcuts are managed in System Settings. The file-association backup made before switching to Neovide is `~/Library/Application Support/Neovide/finder-defaults-before.json`.
