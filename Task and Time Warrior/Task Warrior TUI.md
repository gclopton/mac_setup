
Yep—there are good community UIs for Taskwarrior. The two most popular are full-screen terminal apps (so, “GUI in the terminal”), and there are Web/mobile options too.

If you want something you can install on your Mac in one command, try **taskwarrior-tui**. It gives you a split-pane view, Vim-style keys, multi-select, live filtering, and works directly on your Taskwarrior data. Install with Homebrew, then run `taskwarrior-tui` from your shell. ([Homebrew Formulae](https://formulae.brew.sh/formula/taskwarrior-tui?utm_source=chatgpt.com "taskwarrior-tui — Homebrew Formulae"), [kdheepak](https://kdheepak.com/taskwarrior-tui/installation/?utm_source=chatgpt.com "Installation - taskwarrior-tui - A terminal user interface for ..."))

```bash
brew install taskwarrior-tui
taskwarrior-tui
```

Another solid choice is **VIT (Visual Interactive Taskwarrior)**, a lightweight curses UI maintained by the Gothenburg Bit Factory folks. It’s fast, configurable, and ships in Homebrew as `vit`. Run it with `vit`. ([gothenburgbitfactory.org](https://gothenburgbitfactory.org/vit/?utm_source=chatgpt.com "VIT - Gothenburg Bit Factory"), [Homebrew Formulae](https://formulae.brew.sh/formula/vit?utm_source=chatgpt.com "vit — Homebrew Formulae"))

```bash
brew install vit
vit
```

Prefer a browser? There are self-hosted Web UIs that read your local `.task` data or sync via Taskserver. A popular one is **taskwarrior-webui**; it can auto-sync by calling `task sync` periodically when you point the container at your `.taskrc`/`.task`. ([GitHub](https://github.com/DCsunset/taskwarrior-webui?utm_source=chatgpt.com "DCsunset/taskwarrior-webui: Self-hosted Responsive Web ..."))

On mobile, there’s an actively maintained **TaskWarrior Mobile** app for Android (open source, Flutter) that syncs with your Taskserver, so you can view/edit your tasks on the go. ([Google Play](https://play.google.com/store/apps/details?hl=en_US&id=com.ccextractor.taskwarriorflutter&utm_source=chatgpt.com "TaskWarrior Mobile - Apps on Google Play"))

Bottom line: there isn’t a single “official” GUI, but taskwarrior-tui and VIT are excellent, one-command installs on macOS; if you want a mouse-driven app, a self-hosted Web UI is the way to go, and Android has a capable client. ([GitHub](https://github.com/kdheepak/taskwarrior-tui?utm_source=chatgpt.com "GitHub - kdheepak/taskwarrior-tui"), [Homebrew Formulae](https://formulae.brew.sh/formula/vit?utm_source=chatgpt.com "vit — Homebrew Formulae"), [Google Play](https://play.google.com/store/apps/details?hl=en_US&id=com.ccextractor.taskwarriorflutter&utm_source=chatgpt.com "TaskWarrior Mobile - Apps on Google Play"))


# Taskwarrior-TUI — a friendlier face for Taskwarrior


If you like Taskwarrior’s power but want a fast, navigable screen you can live in, Taskwarrior-TUI (TWTUI) is the sweet spot. It reads your existing `~/.taskrc`, honors your reports, colors, and contexts, and lets you filter, add, complete, edit, and multi-select with tight, Vim-style keys. Install it on macOS with Homebrew (`brew install taskwarrior-tui`), then launch it with `taskwarrior-tui`—or add `alias tt="taskwarrior-tui"` and you’ve got a one-keystroke dashboard. ([Homebrew Formulae](https://formulae.brew.sh/formula/taskwarrior-tui?utm_source=chatgpt.com "taskwarrior-tui — Homebrew Formulae"), [kdheepak](https://kdheepak.com/taskwarrior-tui/quick_start/ "Quick Start - taskwarrior-tui - A terminal user interface for taskwarrior"))

Once it opens, think of TWTUI as “Taskwarrior with a live filter and panes.” Press `?` any time for help. Cycle views with `]` and `[` (for example, the task list and a scrollable calendar). Quit with `q`; `Esc` backs out of prompts. These are the documented defaults, and you can remap almost all of them later. ([kdheepak](https://kdheepak.com/taskwarrior-tui/keybindings/ "Keybindings - taskwarrior-tui - A terminal user interface for taskwarrior"), [Arch Manual Pages](https://man.archlinux.org/man/extra/taskwarrior-tui/taskwarrior-tui.1.en?utm_source=chatgpt.com "taskwarrior-tui(1)"))

Filtering is where the TUI really shines. Hit `/` and type the same filter grammar you’d use at the CLI—`project:paper and +writing and due.before:friday`—and watch the list update as you type. Use ↑/↓ to traverse history, and Tab/Shift-Tab for completions; the prompt supports familiar readline shortcuts (Ctrl-A/E to jump, Ctrl-W to delete a word, etc.). Because it’s the same grammar, everything you learned in the filtering chapter “just works,” only faster. ([kdheepak](https://kdheepak.com/taskwarrior-tui/keybindings/ "Keybindings - taskwarrior-tui - A terminal user interface for taskwarrior"))

Routine actions are single keys. Press `a` to add a task (you’ll get a mini command line), `d` to mark the selected task done, `x` to delete it, `m` to modify attributes in place, `A` to annotate, and `e` to open the current task in your `$EDITOR` via `task edit`. Toggle start/stop with `s`. Tap `z` to reveal a details pane and scroll it with Ctrl-E / Ctrl-Y. All of these keys are the TWTUI defaults. ([kdheepak](https://kdheepak.com/taskwarrior-tui/keybindings/ "Keybindings - taskwarrior-tui - A terminal user interface for taskwarrior"))

Bulk work is frictionless. Move with `j/k`, press `v` to mark a task, repeat to mark several, and then run your action once—`d` completes all marked tasks; `x` deletes them; `m` applies a modification (e.g., `+advisor priority:H`) to the set. `V` toggles marks on the whole filtered list when you really mean “everything I’m looking at.” If you make a mistake, `u` is a standard `task undo`. The status line reflects exactly which `task …` command will be executed on the “selected” set. ([kdheepak](https://kdheepak.com/taskwarrior-tui/keybindings/ "Keybindings - taskwarrior-tui - A terminal user interface for taskwarrior"))

Contexts and reports carry over. Press `c` for a context switcher, or start the TUI on a specific report with `taskwarrior-tui -r review`. If you want a custom default view every time TWTUI launches, set it once in your `~/.taskrc` as `uda.taskwarrior-tui.task-report.next.filter=<report-name>`; under the hood, the TUI parses `task show` so it inherits the same defaults and report definitions you use at the CLI. ([kdheepak](https://kdheepak.com/taskwarrior-tui/configuration/advanced/ "Advanced - taskwarrior-tui - A terminal user interface for taskwarrior"))

Colors and appearance are shared with Taskwarrior. If you already include a theme (for example, `include dark-256.theme`) or have custom color rules like `color.active=white on blue`, the TUI picks them up; you can also tune TWTUI-specific style and UI options through `uda.taskwarrior-tui.*` keys in `~/.taskrc` (selection indicators, scrollbar glyphs, whether the info pane shows by default, and more). The official color and key-configuration guides list all supported switches. ([taskwarrior.org](https://taskwarrior.org/docs/themes/?utm_source=chatgpt.com "Color Themes"), [Manpages Ubuntu](https://manpages.ubuntu.com/manpages/oracular/man5/task-color.5.html?utm_source=chatgpt.com "task-color - A color tutorial for the Taskwarrior command ..."), [kdheepak](https://kdheepak.com/taskwarrior-tui/configuration/colors/?utm_source=chatgpt.com "Color configuration - taskwarrior-tui - A terminal user interface ..."))

A few quality-of-life tricks round it out. Map the “quick-tag” toggle (default key `t`) to whatever tag you use for triage—say `next`—so a single keystroke adds/removes it on the current selection. Bind your own shortcuts 1–9 to shell scripts (for example, `task sync` or a custom bulk edit) via `uda.taskwarrior-tui.shortcuts.N=…`; you can even run one background process periodically (commonly `task sync` every 60 seconds) using `uda.taskwarrior-tui.background_process` and `…_period`. And if you’d like TWTUI to start on a grouping-heavy daily review, just point `-r` (or that default filter key) at your `report.review` from the reports chapter. ([kdheepak](https://kdheepak.com/taskwarrior-tui/configuration/advanced/ "Advanced - taskwarrior-tui - A terminal user interface for taskwarrior"))

In short, Taskwarrior-TUI is not a separate system; it’s your same Taskwarrior, seen through a faster lens. Install, launch, filter with `/`, act with single keys, and let your existing reports, contexts, and colors flow through. When you’re ready to tailor it, everything lives in `~/.taskrc`, and every binding or knob you see here is documented upstream—so you can keep it stable, scriptable, and in sync with the CLI you already trust. ([kdheepak](https://kdheepak.com/taskwarrior-tui/installation/ "Installation - taskwarrior-tui - A terminal user interface for taskwarrior"))


