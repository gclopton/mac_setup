


scroll down through file `j`

Jump to End of Page: `Shift + G`
Jump to Top of Page : `gg`


Relative numbers are perfect for this. In Normal mode, put a **count** before a motion:

- **Up 6 lines:** type `6k` (or `6-` to go to the first non-blank on that line)
    
- **Down 17 lines:** type `17j` (or `17+` to go to the first non-blank)
    

A few related tips:

- Recenter after a jump: `zz`
    
- Select a block while moving: `V17j` (visual-line + 17 lines down)
    
- Absolute line jump: `:42` or `42G`
    
- Page moves: `Ctrl-u` (half page up), `Ctrl-d` (half page down)


# Indenting

Yep — in Vim/Neovim you don’t indent big chunks with Tab. Use the indent **operators** instead:

### Quick ways to indent a lot of code

- **Re-indent the whole file (Lua aware):**
    
    - `gg=G`  
        (go to top, then `=` over the whole buffer using filetype rules)
        
- **Re-indent just a block/selection:**
    
    1. `V` (visual-line mode), move to the last line you want.
        
    2. Press `=` to auto-indent by Lua rules.  
        (Or press `>` to shift right one _shiftwidth_, `<` to shift left.)
        
    
    - Tip: after `>`/`<`, hit `.` to repeat; `gv` reselects the last visual area.
        
- **Indent current line only:**
    
    - `==` (auto-indent this line)
        
    - `>>` (shift right one level), `<<` (shift left)
        
- **While in Insert mode at line start:**
    
    - `Ctrl-t` indent one level, `Ctrl-d` outdent.
        

### Why Tab “does nothing”

In Insert mode your Tab is probably mapped for completion/snippets (LazyVim/cmp/LuaSnip), so it won’t shift lines. Use the Normal/Visual mode commands above (`Esc` first).

### Sanity check for Lua indentation

Make sure the buffer is detected as Lua: `:set ft?` → should say `filetype=lua`. Then `gg=G` should clean it up in one shot.