# Your first editing session

Neovide provides the window, Neovim edits the text, and LazyVim supplies much of your current configuration. Opening a file in Neovide uses the same Neovim configuration as opening it in a terminal. The graphical interface can feel different even though the editing commands are the same. [Neovide documentation](https://neovide.dev/)

## How do I type, save, and get out?

Neovim separates typing from editing commands. Press `Esc` to enter Normal mode. Press `i` to enter Insert mode and type text. Press `Esc` again when you want to issue an editing command.

Practice on the file `scripts/link-practice.py` in this vault, rather than on a research source.

1. Open the file in Neovide from Finder.
2. Press `i`, type a short comment, then press `Esc`.
3. Type `:w` and press Enter to save.
4. Type `:q` and press Enter to close the current window. Neovim will warn if that window contains unsaved changes.

Use `:wq` to save and close. Use `:q!` only when you deliberately want to discard unsaved changes in the current buffer.

## How do I move without memorizing everything?

The arrow keys work. You can gradually add `h`, `j`, `k`, and `l` for left, down, up, and right in Normal mode. `gg` goes to the beginning and `G` to the end. Type `/radius`, press Enter, and use `n` to visit the next match.

## How do I undo or discover a command?

In Normal mode, `u` undoes an edit and `Ctrl-r` redoes it. Your LazyVim setup uses Space as its leader key: press Space and pause to see available shortcuts. A sequence such as **Space, o, l** means three successive keypresses, not holding all three at once.

The built-in `:Tutor` command starts Neovim's introductory tutorial. Use `:help` for reference documentation. [Neovim first steps](https://neovim.io/doc/user/usr_02.html)

Next: [[NVIM/Code Links - Follow a link and copy an excerpt|Follow a code link and make an excerpt]].

## Practice reference

Start in Normal mode unless the instruction says otherwise. `Esc` returns from Insert mode; `i` begins inserting before the cursor. `5j` moves down five lines, `w` moves to the next word beginning, `gg` goes to the first line, and `G` to the last.

`V` starts a whole-line selection. With the cursor at the beginning of `radius area volume`, `dw` removes `radius `; `dd` removes the entire current line. Both combine an editing action with a span of text. `u` undoes the edit, and `Ctrl-r` redoes it. `:w` followed by Enter saves without closing.

Practice these in [[NVIM/First keys - practice|First keys — practice]], then try them in `NVIM/practice.txt`. The built-in help topics `:help motion.txt`, `:help deleting`, and `:help undo` explain their exact behavior.
