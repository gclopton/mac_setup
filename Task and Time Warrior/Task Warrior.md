

1. The Taskwarrior mental model — What a “task” really is (UUIDs, attributes, reports) and why Taskwarrior favors plain text, composable commands, and repeatable workflows.
    
2. Install, upgrade, and first run (macOS-centric) — Homebrew install, verifying the binary, and what gets created in `~/.task`/`~/.taskrc`.
    





# 1. The Taskwarrior Mental Model

Think of Taskwarrior as a tiny, fast database with a command-line lens. Each task is a structured record (not just a string) with fields—_attributes_—that you can query, sort, and mutate. The CLI glues three ideas together: you **filter** a set of tasks, run a **command** on that set, and optionally apply **modifiers** (attribute updates). With that picture in mind, everything else feels natural.

## The core object: a task (a structured record)

A task is a record with a unique identifier, a human description, and typed attributes. The obvious attributes are things like `description`, `project`, `tags`, `due`, `priority`, and `status`; but there are many more, including dates (`entry`, `modified`, `scheduled`, `wait`), relationships (`depends`), and computed values (like _urgency_). You can attach free-form notes via **annotations**, which travel with the task and remain searchable.

```bash
# Add a fully-formed record in one line
task add "Draft Fig. 2 (plasmon dispersion)" project:paper +writing due:fri priority:H
```

Behind the scenes, Taskwarrior stores this as a structured item on disk and updates indexes/metadata so you can retrieve it instantly with expressive queries.

## Identity: ephemeral ID vs stable UUID

When you list tasks interactively, Taskwarrior shows a small integer **ID** in the first column. That number is ephemeral: it’s just the row number _in that view at that moment_. For quick interactive work—“mark #3 done”—it’s perfect. For anything scripted, automated, or long-lived, use the **UUID**, a globally unique, permanent identifier stored with the task.

```bash
# Interactive: ID is convenient
task list
task done 3

# Script-safe: grab and use a UUID
task 3 info            # shows the UUID
task <UUID> modify +urgent
```

Hold this rule tightly: ID for hands-on sessions; UUID for everything programmatic.

## Attributes are the language of work

Attributes aren’t mere labels—they _drive behavior_. A few illustrative ones:

- `project:` groups tasks into a hierarchy you can slice (`project:paper`, `project:course/ra`).
    
- `+tag` and `-tag` add/remove cross-cutting labels (`+writing`, `+review`, `-blocked`).
    
- `due:` influences sorting and urgency, accepts natural dates (`due:tomorrow`, `due:2025-09-01`).
    
- `scheduled:` says “not now, but make it appear then” without making it late.
    
- `wait:` hides a task until a date/time; useful for deferring.
    
- `depends:` encodes prerequisites so blockers are obvious and sorting stays sane.
    
- `priority:` is a coarse override (H/M/L) that still plays alongside computed urgency.
    

You can set attributes at creation, or later with `modify`:

```bash
task <UUID> modify project:paper +analysis due:nextfri
task <UUID> annotate "Need updated spectra from last week’s run."
task <UUID> modify depends:<other-uuid>
```

## The grammar: filter → command → modifiers

Every invocation follows the same mental template:

```
task [FILTER EXPRESSION] COMMAND [ATTRIBUTE MODIFIERS]
```

- **Filter**: a query that selects a _working set_. Filters are just boolean expressions over attributes: `project:paper and +writing and due.before:sunday`, `status:pending and -tagged`, `priority:H or due.before:today`. If you omit a command, Taskwarrior runs your default report (often `next`) on that filter.
    
- **Command**: what you want to _do_ with the set—`list`, `info`, `add`, `modify`, `done`, `delete`, `start`, `stop`, `annotate`, `export`, and more.
    
- **Modifiers**: attribute updates applied to the matched set (for mutating commands).
    

```bash
# Read: “List pending paper tasks tagged writing due before Sunday”
task project:paper +writing due.before:sunday list

# Read: “For all urgent paper tasks, push due date to tomorrow and add a tag”
task project:paper priority:H modify due:tomorrow +now
```

This _filter → command → modifiers_ pipeline is the beating heart of Taskwarrior. Master it, and the rest is ornamentation.

## Reports are saved views (your living dashboards)

A **report** is a named, reusable view: a saved filter + column layout + sort order. Reports turn ad-hoc queries into rituals—your “Next,” “Waiting,” “Today,” “Review,” or “Paper-Sprint” dashboards—so you don’t retype logic daily. Under the hood, each report is a few lines in your config specifying which columns to show, how to sort, and (optionally) a default filter. Running `task next` or `task waiting` is just invoking a saved lens on the same task database.

The key mindset: you aren’t “printing lists”; you’re curating _lenses_.

## Lifecycle is explicit

Tasks move through clear states—`pending` → `completed` (or `deleted`)—with optional `started`/`stopped` intervals if you track in-progress work. `waiting`/`scheduled` temporarily hide items from normal views without losing them. Dependencies mark blockers; recurrence spawns future instances on completion. Because state is explicit, reviews and automation become predictable rather than ad-hoc.

```bash
task start 2          # begin work (adds a start timestamp)
task stop 2           # pause (records elapsed)
task done 2           # complete (moves to history)
task undo             # oops-safe: revert the last change
```

## Why plain text and composition win

Taskwarrior’s data is local and transparent, which buys you three superpowers:

1. **Interoperability**: `task export` streams clean JSON, so you can pipe into `jq`, Python, or R for custom analytics, then `task import` to round-trip safely.
    
2. **Composability**: filters are a real query language; the CLI plays well with shells, cron/launchd, and editors. It behaves like a Unix tool—predictable in scripts, comfortable in muscle memory.
    
3. **Repeatability**: saved reports, contexts, and your `.taskrc` encode your workflow as code. Your setup is auditable, portable, and easy to evolve as your work changes.
    


If you keep only one picture from this chapter, make it this: _Taskwarrior is a structured database of tasks; you select a working set with a filter, act on it with a command, and shape what you see with reports._ Everything else—tags, due dates, dependencies, urgency, hooks—are just richer attributes and smarter views riding on that core model.


# 2 Installation with Homebrew

On macOS you can get Taskwarrior running very quickly since it’s already packaged in Homebrew. Here’s a clean way to set it up:

---

### 1. Install Taskwarrior

If you don’t already have [Homebrew](https://brew.sh/) installed, do that first:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install Taskwarrior:

```bash
brew install task
```

This will give you the latest stable version.

---

### 2. Verify the installation

Check the version to confirm:

```bash
task --version
```

You should see something like `2.6.x` or `3.x` depending on what’s current.

---

### 3. First run and basic usage

Taskwarrior creates a data directory the first time you run it. Try adding a task:

```bash
task add Buy groceries
```

Then list tasks:

```bash
task list
```

Mark one as done:

```bash
task done 1
```

(The number is the task ID shown in `task list`).

---

### 4. Configuration

Taskwarrior stores config in:

- `~/.taskrc` → main config file
    
- `~/.task/` → data files
    

You can tweak defaults by editing `~/.taskrc`. For example, to change the default report or color theme.

---

### 5. Optional: Syncing across devices

If you want cloud sync:

- Set up [Taskserver](https://taskwarrior.org/docs/taskserver.html) (self-hosted, also MIT licensed).
    
- Or, if you want something simpler, just sync the `~/.task` directory with iCloud/Dropbox/Git.
    

---

### 6. Optional: Integrations

- **JSON import/export**: `task export` and `task import` for programmatic access.
    
- **Hooks and scripts**: You can write custom hooks in Python, Bash, etc.
    
- **UIs**: Some people use [Taskwarrior-TUI](https://github.com/kdheepak/taskwarrior-tui) for a curses interface.
    



# 3. Your first five commands — add, list, modify, done, delete

These five verbs are your daily loop. Remember the mental model: **filter → command → modifiers**. In examples below, the leftmost number is the ephemeral **ID** shown by a report; for anything scripted, prefer the **UUID** from `task <ID> info`. ([Taskwarrior](https://taskwarrior.org/docs/syntax/?utm_source=chatgpt.com "Command Line Syntax"))

## `add` — create a complete task in one breath

You can set structure (project, tags, dates, priority) and the human description at once. Order doesn’t matter, but quote multi-word descriptions.

```bash
# A small sandbox
task add "Draft Fig. 2 (plasmon dispersion)" project:paper +writing due:fri priority:H
task add "Email committee about proposal feedback" +email +admin project:phd due:mon
task add "Refactor simulation script for CeO2 runs" project:lab +code scheduled:tomorrow
task add "Buy coffee beans" +errand
task add "Go to the library" 
```

Weekday names like `fri`, `mon` mean the **next** occurrence of that day at 00:00 local; `tomorrow` is also valid. You can always use ISO dates (`YYYY-MM-DD`) or relative durations such as `2days`, `1w`. ([Taskwarrior](https://taskwarrior.org/docs/named_dates/ "Taskwarrior - Named Dates - Taskwarrior"))

## `list` — see the current working set

`list` shows pending tasks that match your filter (or all pending if you omit one). The filter comes **before** the command.

```bash
task list
task project:paper list
task +email due.before:tomorrow list
task +email due.before:saturday list
task due.today list
```

The `before/after` attribute modifiers let you make crisp date filters like `due.before:tomorrow`. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/?utm_source=chatgpt.com "task.1 for task-3.4.1"))

## `modify` — change attributes as your plan evolves

Use `modify` to adjust tags, dates, priority, projects—or replace the description. Tags are added/removed with `+tag` / `-tag`. Description changes are simply the new text at the end.

```bash
# Add a tag and move the deadline to the next Friday
task 1 modify +analysis due:fri

# Remove a tag and lower priority
task 2 modify -admin priority:M

# Move to a subproject and schedule (a softer "make relevant on") for Wednesday
task 3 modify project:lab/tools scheduled:wed -code +tooling

# Replace the human-readable description
task 1 modify "Draft Fig. 2 (update plasmon dispersion after new spectra)"

# Quick note without touching the description
task 1 annotate "Need updated spectra from last week’s run."
```

All of the above forms are straight from the command syntax: filter first (`1`, `2`, `3`), then `modify`, then your changes. Named weekdays like `wed` and relative durations like `2days` are valid date inputs. ([Taskwarrior](https://taskwarrior.org/docs/syntax/?utm_source=chatgpt.com "Command Line Syntax"))

## `done` — finish and keep the record

Mark finished work as completed. This sets status to `completed`, records an `end` timestamp, and drops the task from pending views (it remains in history).

```bash
task 4 done
```

If you track “in progress” time, wrap with `start`/`stop`:

```bash
task 3 start
# ...work...
task 3 stop
task 3 done
```

`undo` will revert the last change, including an accidental `done`. ([Taskwarrior](https://taskwarrior.org/docs/commands/done/?utm_source=chatgpt.com "Done Command"))

## `delete` — remove a task you don’t want to exist

Delete is for mistakes, not de-prioritized work. It marks a task as `deleted` (distinct from `completed`) and, by default, asks for confirmation.

```bash
task 2 delete
```

That confirmation prompt is controlled by `confirmation=1` in `~/.taskrc` (on by default). If you meant to keep the audit trail, prefer `done`. ([Debian Manpages](https://manpages.debian.org/unstable/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior — Debian unstable"))

---

### A quick end-to-end example

```bash
# Add it (explicit ISO date also works)
task add "Submit abstract to MMM conference" project:paper +writing due:2025-09-15

# See it
task project:paper +writing list

# Plans shift—make it appear in your views on September 1 and capture context
task 1 modify scheduled:2025-09-01
task 1 annotate "Need advisor sign-off before submission."

# Finish it
task 1 done
```

That’s the whole loop: **add → list → modify → done/delete**. Once these are muscle memory, reports, contexts, urgency tuning, and dependencies slide neatly into place. If a date token ever looks ambiguous, sanity-check it with `task calc <token>` (e.g., `task calc fri`) before you commit. ([Taskwarrior](https://taskwarrior.org/docs/named_dates/ "Taskwarrior - Named Dates - Taskwarrior"))



# 4. Projects, tags, and annotations — Lightweight structure for grouping, cross-cutting labels, and keeping rich notes on tasks.


Taskwarrior gives you one **project** per task for hierarchical grouping, any number of **tags** for cross-cutting labels, and free-form **annotations** for rich, searchable notes. Master these three and you’ll have just enough structure to keep big research workstreams and small daily todos in the same system without friction.

**Projects** are a single string attribute on a task. Set them when you add a task or later via `modify`, and filter with `project:<value>` before a command. Projects don’t have built-in hierarchy semantics, but many people encode subprojects by dot notation (e.g., `project:paper.revisions`) and then use the string operators for exact/partial matching. For example, `project.is:paper.revisions` matches exactly; `proj.startswith:paper.` finds all “children”; and `proj.endswith:revisions` finds any project name that ends with “revisions.” ([Taskwarrior](https://taskwarrior.org/docs/filter/ "Taskwarrior - Filters - Taskwarrior"))

```bash
# Set a project at creation or later
task add "Draft Fig. 2 (plasmon dispersion)" project:paper +writing
task 1 modify project:paper.revisions

# Filter by project (exact vs partial)
task project:paper list                 # any task whose project contains “paper”
task project.is:paper.revisions list    # exact match
task proj.startswith:paper. list        # treat dot as “hierarchy” by convention
```

Two project reports help you see the big picture. `task projects` lists projects (with counts) matching your current filter, and `task summary` shows a per-project breakdown of pending/completed statuses—great for weekly reviews. Add `rc.list.all.projects=1` if you also want to see projects that currently have no pending tasks. ([Taskwarrior](https://taskwarrior.org/docs/commands/?utm_source=chatgpt.com "the Commands"), [Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/640334/taskwarrior-question-howto-show-completed-projects?utm_source=chatgpt.com "Taskwarrior Question : Howto show completed projects?"))

**Tags** are zero or more single-word labels per task. Add and remove them with `+tag` and `-tag` either at creation or with `modify`; filter with the same `+tag`/`-tag` syntax. A tag must be one word that does not start with a digit, punctuation, or a math operator. In practice, keep your own tags lowercase to avoid clashing with **virtual tags**, which are uppercase shortcuts like `+PENDING`, `+OVERDUE`, `+TODAY`, etc. ([Taskwarrior](https://taskwarrior.org/docs/filter/ "Taskwarrior - Filters - Taskwarrior"))

```bash
# Add/remove/filter with tags
task add "Email committee about proposal feedback" +email +admin project:phd
task 2 modify -admin +faculty
task +email project:phd list

# Tag overview
task tags
```

**Annotations** are time-stamped notes attached to a task. They’re perfect for meeting links, URLs, quick status snippets, or “why” context you don’t want in the description. Add with `annotate`, remove with `denotate`, and filter for “things that have notes” with the virtual tag `+ANNOTATED`. You can also add a note “en-passant” while completing a task by appending text after `done`. ([Taskwarrior](https://taskwarrior.org/docs/commands/add/?utm_source=chatgpt.com "Add Command - Taskwarrior"))


```bash
# Add and view notes
task 1 annotate "Need updated spectra from last week’s run"
task 1 info
```

```bash
# Add and remove notes
task 1 annotate "Need updated spectra from last week’s run"
task 1 denotate "Need updated spectra from last week’s run"

# Find anything that has notes
task +ANNOTATED list

# Add a note while completing
task 1 done "Submitted revised figure with new dispersion data"
```

A few patterns keep this simple and robust. Favor one **project** per task that mirrors the main deliverable (e.g., `project:paper`), then let **tags** carry contexts (`+writing`, `+analysis`, `+email`) and people (`+advisor`, `+committee`). Put URLs, meeting notes, and small status breadcrumbs in **annotations** so your reports stay clean—most list views can show a count instead of dumping full notes, and you can always open `task <ID> info` for the details. This gives you fast, precise filters in daily use, while keeping the richer narrative attached and searchable when you need it. ([Taskwarrior](https://taskwarrior.org/docs/commands/list/?utm_source=chatgpt.com "List Report - Taskwarrior"))


# 5. Dates that drive behavior — `due`, `scheduled`, `wait`, start/stop times, recurrences, and when to prefer each.


Taskwarrior becomes dramatically more useful once you let dates steer visibility and urgency. Three attributes shape _when_ a task should appear in your working views—`due`, `scheduled`, and `wait`—and two commands, `start`/`stop`, mark actual work. Recurrence (`recur`, with an optional `until`) then turns one task into a series. All of this is first-class, documented behavior—not magic strings. ([Taskwarrior](https://taskwarrior.org/docs/using_dates/ "Taskwarrior - Using Dates Effectively - Taskwarrior"))



 
## `due:` — the hard deadline.

Use `due` for the last acceptable moment to finish. It’s queryable (`+TODAY`, `+DUE`, `overdue` report) and influences sorting/urgency in most reports. If you omit a time of day, it defaults to **00:00:00 local**—use an ISO timestamp when you need a precise hour. Example:

```bash
task add "Submit RA problem set" project:analysis due:2025-09-01T17:00
```

Taskwarrior accepts ISO-8601 (`YYYY-MM-DDThh:mm`) regardless of your `rc.dateformat`, and also named dates like `monday`, `fri`, `tomorrow`. ([Taskwarrior](https://taskwarrior.org/docs/dates/ "Taskwarrior - Date &amp; Time - Taskwarrior"))


tomorrow 


## `scheduled:` — the earliest sensible start.**  

Use `scheduled` to say “don’t show this as _ready_ until then.” After the scheduled moment passes, the task gains the virtual tag `+READY` and appears in `task ready`. Keep `due` for the true deadline; keep `scheduled` for “work becomes relevant on…”. Example:

```bash
task add "Draft Fig. 2 methods paragraph" project:paper scheduled:wednesday due:friday
```

Tasks with no `scheduled` date are considered always ready; adding one gates their appearance in “ready” views. ([Taskwarrior](https://taskwarrior.org/docs/using_dates/ "Taskwarrior - Using Dates Effectively - Taskwarrior"))




## `wait:` — hide this until a date.

`wait` temporarily removes a task from normal pending lists—useful for deferrals and to reduce noise. You can combine it with `due` and `scheduled`, and even set it _relative_ to `due`. Example:

```bash
task add "Email committee reminder" due:2025-09-10 scheduled:2025-09-05 wait:due-2days
```

Waiting tasks live in the `waiting` report and carry the `+WAITING` virtual tag; because they aren’t pending, use the `all` report with that tag if a default report filters by `status:pending`. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"))

```bash
task waiting
```

You can remove the waiting attribute as follows:

```bash
task 7 modify wait:
```



**Start/stop times — mark active work (and integrate with Timewarrior).**  
`task <filter> start` stamps a `start` time and makes the task “active” until you `stop` or complete it. This is distinct from completion and can be used alone or to drive **Timewarrior** (official time-tracking companion) via the standard hook: start/stop in Taskwarrior triggers tracking in Timewarrior automatically. Examples:

```bash
task 3 start
# ...work...
task 3 stop
```

See `task active`/`task start`/`task stop` in the manual, and the FAQ note about the Timewarrior hook. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/?utm_source=chatgpt.com "task.1 for task-3.4.1"))

**Recurring tasks — `recur` (with optional `until`).**  
A recurring task is a _template_ (hidden, status `recurring`) that spawns visible instances on your schedule. You specify a `due` and a `recur` interval (e.g., `weekly`, `monthly`, `2w`), and optionally cap the series with `until`. By default, Taskwarrior materializes the next **one** instance ahead (`recurrence.limit=1`), which you can increase if you like seeing further into the future. Examples:

```bash
# Weekly seminar reminder (9:00 on Mondays), for one semester
task add "Analysis seminar" due:2025-09-01T09:00 recur:weekly until:2025-12-15

# Lab meeting: show 15 minutes beforehand, every Thursday
task add "Lab meeting" due:2025-09-04T14:00 recur:weekly wait:due-15min
```

This is the documented pattern: the template stays hidden; instances inherit description/tags/project; and `recurrence.limit` controls how many upcoming instances appear. ([Taskwarrior](https://taskwarrior.org/docs/recurrence/ "Taskwarrior - How Recurrence Works - Taskwarrior"))

**Named dates and exact times, without surprises.**  
You can use weekday and month names (`monday`, `sat`, `november`), “calendar” synonyms like `eom` (end of month), and ISO datetimes. If you don’t specify a time, it’s midnight; for precise class/meeting times, include `Thh:mm`. Sanity-check any token with `task calc` before you commit. ([Taskwarrior](https://taskwarrior.org/docs/dates/ "Taskwarrior - Date &amp; Time - Taskwarrior"))

**When to prefer each (rules of thumb).**  
Use `due` only for true deadlines; they drive urgency and overdue logic. Use `scheduled` when you want something to _become_ ready at a moment but not be late if you miss that moment. Use `wait` to hide tasks until they’re even worth seeing (often relative to `due`, like `wait:due-2days`). Use `start`/`stop` to reflect real work in progress (and to hand off timing to Timewarrior). Use `recur` + `until` for classes, recurring meetings, bills, and weekly chores—set a concrete `due` timestamp for each occurrence and, if you want pre-notice in your lists, add `wait:due-<offset>`. ([Taskwarrior](https://taskwarrior.org/docs/using_dates/ "Taskwarrior - Using Dates Effectively - Taskwarrior"))

**A compact recipe you can reuse.**

```bash
# Weekly class, visible 10 minutes before start, through Dec 15
task add "Real Analysis (Tue)" \
  project:school +class \
  due:2025-09-02T09:00 recur:weekly until:2025-12-15 wait:due-10min

task add "Real Analysis (Thu)" \
  project:school +class \
  due:2025-09-04T09:00 recur:weekly until:2025-12-15 wait:due-10min
```

This keeps your “ready” views calm until it’s time to leave for class, and it’s all done with built-ins—no guesswork. ([Taskwarrior](https://taskwarrior.org/docs/using_dates/ "Taskwarrior - Using Dates Effectively - Taskwarrior"))




# 6. Bulk finishing and bulk deletion in Taskwarrior

**Done vs. delete.** “Done” moves a task to `completed` (kept in history); “delete” moves it to `deleted` (meant for mistakes). Both accept filters, so you can operate on dozens at once. The core pattern is always: **filter → command**. For example, to complete all overdue items:

```bash
task +OVERDUE done
```

Virtual tags like `+OVERDUE`, `+TODAY`, `+WAITING`, `+DUE` are built in and expand to time/status logic at runtime, which is why they’re ideal for bulk operations. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"))

To delete a whole slice—say, everything in a project:

```bash
task project:old-thesis delete
```

If you want to delete “almost everything,” negate a virtual tag. This wipes all non-deleted tasks regardless of state (pending, completed, waiting, recurring instances), so use with care:

```bash
# Danger: selects everything except what's already deleted
task -DELETED delete
```

The `delete` and `done` commands are first-class Taskwarrior subcommands. ([Taskwarrior](https://taskwarrior.org/docs/commands/?utm_source=chatgpt.com "the Commands"))

## Seeing what you will affect first

Before you swing the hammer, count:

```bash
task +OVERDUE count
task project:old-thesis count
```

The count applies your filter to the full database, not just to what the default report shows. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/?utm_source=chatgpt.com "task.1 for task-3.4.1"))

## Safety nets: confirmations, bulk threshold, and undo

Taskwarrior protects you by prompting on large changes and risky cases:

- **Confirmation prompts.** By default, Taskwarrior asks before deletes and other bulk changes (`confirmation=yes`). You can toggle it explicitly or one-off at the command line:
    

```bash
task config confirmation off   # persistent off (be careful)
task rc.confirmation=off ...   # one command only
```

Default behavior and the `confirmation` knob are documented. ([Ubuntu Manpage](https://manpages.ubuntu.com/manpages/xenial/man5/taskrc.5.html?utm_source=chatgpt.com "taskrc - Configuration details for the task(1) command"), [Taskwarrior](https://taskwarrior.org/docs/configuration/?utm_source=chatgpt.com "Configuration"))

- **Bulk threshold.** Taskwarrior will also prompt when a command touches “many” tasks—controlled by `rc.bulk` (value `0` means “infinite,” i.e., never consider anything ‘bulk’). Use runtime overrides if you’re intentionally changing a lot:
    

```bash
task rc.bulk=0 rc.confirmation=off +OVERDUE done
```

The purpose of `rc.bulk` and the special value `0` are documented in the manual and release notes. ([Taskwarrior](https://taskwarrior.org/docs/commands/modify/?utm_source=chatgpt.com "Modify Command"))

- **Undo.** If you go too far:
    

```bash
task undo
```

`undo` rolls back the most recent write operation and obeys the confirmation setting. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/?utm_source=chatgpt.com "task.1 for task-3.4.1"))

## Purging: permanent removal

Deleting only flips status to `deleted`. **Purging** removes tasks from the data files entirely:

```bash
# Purge only those already marked deleted
task +DELETED purge
```

`purge` is a dedicated command (added in 2.6.0) for permanent cleanup. ([Taskwarrior](https://taskwarrior.org/docs/commands/?utm_source=chatgpt.com "the Commands"))

If you want a full “empty database” flow:

```bash
# 1) Mark everything (except already-deleted) as deleted
task -DELETED delete
# 2) Permanently remove deleted tasks
task +DELETED purge
```

(If you sync between devices, do the same cleanup everywhere; purge is local.)

There’s also a config option on some builds to auto-purge “old deleted” tasks after sync (`purge.on-sync`), with “old” typically meaning deleted and untouched for 180 days. ([manpages.opensuse.org](https://manpages.opensuse.org/Tumbleweed/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior"))

## Working with waiting tasks in bulk

“Waiting” tasks (hidden until their `wait` date) are easy to target:

```bash
# See them
task waiting
# Mark them all done (yes, even if they’re still waiting)
task +WAITING done
# Or remove the wait gate from all of them
task +WAITING modify wait:
```

The `waiting` report, `+WAITING` virtual tag, and `wait:` semantics are all part of core Taskwarrior. ([Taskwarrior](https://taskwarrior.org/docs/terminology/?utm_source=chatgpt.com "Taskwarrior - Terminology"))

## Recurring tasks: instances vs. the template

A recurring series has a hidden **template** (status `recurring`) and visible **instances** you complete week by week. If you bulk-finish or bulk-delete instances, the template will keep generating future ones. To stop the series, operate on the parent/template:

```bash
# Find templates
task all +PARENT list
# Delete the template to stop new instances
task all +PARENT delete
```

Recurring behavior (template vs. instances) and the `+PARENT`/recurrence model are documented; Taskwarrior also shows only a limited number of future instances (`recurrence.limit`, default 1) to avoid clutter. ([Taskwarrior](https://taskwarrior.org/docs/recurrence/?utm_source=chatgpt.com "How Recurrence Works"))

You can adjust how aggressively Taskwarrior asks about propagation when changing recurring tasks via:

```bash
# 'prompt' (default) | 'yes' (auto-propagate) | 'no' (never propagate)
task config recurrence.confirmation prompt
```

That setting is called out in the manuals. ([Ubuntu Manpage](https://manpages.ubuntu.com/manpages/focal/man5/taskrc.5.html?utm_source=chatgpt.com "taskrc - Configuration details for the task(1) command"))

## Focused bulk edits: ranges and precise filters

Ranges and rich filters help you bulk-operate precisely:

```bash
# A numeric range from a list you just ran
task 10-25 done

# Complete everything due today
task +TODAY done

# Delete only completed tasks in a project (true cleanup)
task project:old-thesis status:completed delete
```

Virtual tags and attribute filters are the intended way to target slices like “today,” “overdue,” or “completed in project X.” ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"))

## One-off “I know what I’m doing” mode

Sometimes you want zero interruptions—for example, after a migration or import:

```bash
task rc.confirmation=off rc.bulk=0 rc.recurrence.confirmation=yes -DELETED delete
task +DELETED purge
```

These are standard runtime overrides; they don’t change your `~/.taskrc`. Use with caution. ([Taskwarrior](https://taskwarrior.org/docs/configuration/?utm_source=chatgpt.com "Configuration"))

---

## A few ready-made recipes

Mark everything overdue as finished:

```bash
task +OVERDUE done
```

Delete all waiting tasks in a specific project:

```bash
task +WAITING project:tmp-sandbox delete
```

Clear all tasks (including completed/waiting/recurring instances), then purge:

```bash
task -DELETED delete
task +DELETED purge
```

Stop a recurring series from generating future instances:

```bash
task all +PARENT delete
```

All of the above use documented commands, virtual tags, and settings—so they’re safe, predictable, and scriptable once you confirm your filters with `count` first. ([Taskwarrior](https://taskwarrior.org/docs/commands/?utm_source=chatgpt.com "the Commands"))

If you want, I can adapt these to your exact workflow—for example, a “Friday cleanup” command that completes all `+READY` items due by Monday, deletes anything in a `+sandbox` tag, purges old deleted tasks, and leaves a short summary.



# 7. Filtering and search grammar — The expressive query language: attribute filters, ranges, regex, logical operators, and saved filters.


Taskwarrior’s power comes from its filter grammar. Every command starts with an optional **filter**, then a **command**, then any **modifications**. Filters are made of terms (tags, attributes, patterns) joined by logical operators. If you provide no command, your default report runs with the filter you supplied. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

## Presence, tags, and simple terms

A bare word in a filter means “find this in the description or annotations.” For example, these are equivalent:

```bash
task garden list
task /garden/ list
task description.contains:garden list
```

Taskwarrior treats the bare word as `description.contains:…` and, by default, regex is enabled so `/…/` is a true regular expression. Case sensitivity is configurable (`search.case.sensitive`). ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

Tags are unary filters: `+work` requires the tag, `-work` forbids it.

```bash
task +work -travel list
```

That idiom—`+tag`/`-tag`—is the canonical way to include/exclude tasks by tag. ([Taskwarrior](https://taskwarrior.org/docs/filter/?utm_source=chatgpt.com "Filters"))

## Attribute filters (strings, dates, numbers)

The most common form is `attribute:VALUE`. Some string attributes have “sugar” defaults:

- `project:Home` means **starts with** “Home” (same as `project.startswith:Home`). Use `project.is:Home` for an exact match. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))
    
- `description:foo` behaves like `description.contains:foo`; `description.word:foo` matches only whole words. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))
    

Dates accept rich _modifiers_:

```bash
# due before end-of-month; inclusive form uses 'by'
task due.before:eom list
task due.by:eom list
```

These are the documented modifiers: `before`/`after` (synonyms `under`/`below`, `over`/`above`), `by` (inclusive), `none`, `any`, `is`/`isnt`, `has`/`hasnt`, `startswith`/`endswith`, `word`/`noword`. They apply to normal attributes and to calculated ones like `urgency`. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

You can also write **relational expressions** with operators and parentheses:

```bash
# Either due before EOM or medium-or-higher urgency, grouped explicitly
task '( due < eom or urgency >= 10 )' list
```

Parentheses matter because most built-in reports already add a hidden filter (for example, `report.list.filter` is `status:pending`), and filter terms are implicitly **AND**ed—so use parentheses with `or`/`xor` to avoid precedence surprises. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

> Tip: shells interpret `<`/`>`; either quote the whole expression (as above) or use the `before`/`after` modifiers to avoid escaping. ([Taskwarrior](https://taskwarrior.org/support/faq/?utm_source=chatgpt.com "Taskwarrior - FAQ"))

## Ranges, IDs, and UUIDs

You can target tasks directly by ID/UUID, including **ranges**:

```bash
task 5 7-10 ebeeab00-ccf8-464b-8b58-f7f2d606edfb info
```

That pattern works for any command; it’s an official part of the filter grammar. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

## Regular expressions (and when they’re literal)

Regex search uses slashes: `/pattern/`. It matches in descriptions and annotations and is enabled by default (`regex=on` in 2.4.0+). If you turn regex off, the same `/…/` becomes a literal match. Control case behavior with `search.case.sensitive`. ([Taskwarrior](https://taskwarrior.org/docs/searching/?utm_source=chatgpt.com "Searching"))

```bash
# Regex: meetings, British or American spelling
task '/m(e|ee)t(ing|ings)/' list
```

## Logical operators and precedence

Filters use `and`, `or`, `xor`, and `!` (not). Terms are **implicitly ANDed**, so this:

```bash
task project:Home +weekend garden list
```

means “project starts with ‘Home’ **and** tag ‘weekend’ **and** description contains ‘garden’.” Use parentheses whenever you combine with `or`/`xor`. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

## Saved filters you can reuse

There are two ways to “save” filters so you don’t retype them:

1. **Contexts** — persistent, named filters that auto-apply to most read/write commands. Define and switch them like this:
    

```bash
task context define school '+school or +homework'
task context school      # activate
task context none        # clear
```

The manual lists exactly which commands are affected (for example, `list`, `add`, `done`, `projects`, `purge`, …). ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

2. **Report filters** — give a custom report its own baked-in filter:
    

```bash
# One-time configuration
task config report.review.columns 'id,project,description'
task config report.review.sort    'project+/,entry+'
task config report.review.filter  'status:pending and +important'

# Use it any time
task review
```

Reports support `columns`, `labels`, `sort`, and `filter`; the docs show the full pattern end-to-end. ([Taskwarrior](https://taskwarrior.org/docs/report/ "Taskwarrior - Reports - Taskwarrior"))

## Virtual tags (quick time windows)

Virtual tags like `+TODAY`, `+DUETODAY`, `+OVERDUE`, `+WAITING`, `+READY`, `+MONTH`, `+WEEK`, etc., expand to date/status logic at runtime. They’re ideal when you don’t want to spell out the full range expression:

```bash
task +DUETODAY list
task +OVERDUE and project:paper next
```

You can tune what counts as “due soon” with `rc.due`; the docs enumerate the common virtual tags. ([Taskwarrior](https://taskwarrior.org/docs/using_dates/?utm_source=chatgpt.com "Using Dates Effectively"))

---

### Pocket reference (doc-correct examples)

```bash
# String matching
task project.is:paper.revisions list         # exact project
task project.startswith:paper. list          # prefix match
task description.word:"boundary layer" list  # whole-word match

# Date windows
task due.by:eom list                         # inclusive through end-of-month
task '( scheduled >= today and scheduled < tomorrow )' list
task entry.after:today-4days list            # examples page

# Regex vs literal
task '/(plasmon|phonon)/' list
task rc.search.case.sensitive=no /meeting/ list

# Ranges and IDs
task 10-25 done
task $(task +OVERDUE ids) modify priority:H
```

All of these forms come straight from the official manual pages and docs, with operators, attribute modifiers, and report/context behavior exactly as specified. ([Taskwarrior](https://taskwarrior.org/docs/man/task.1/ "Taskwarrior - task.1 for task-3.4.1 - Taskwarrior"))

If you want, I can bundle a few of your frequent slices (e.g., “paper-related due this week but **not** waiting”) into a custom `review` report or a `school` context so they’re a single keystroke.



# 8. Sorting, columns, and “reports” — Reading output like a pro, customizing columns, and creating durable, named reports for daily use.



Taskwarrior’s output is just a _report_: a named view with three dials—**columns**, **sort**, and an optional **filter**—plus human-friendly **labels**. The built-in reports (like `next`, `list`, `long`) are ordinary reports you can inspect, clone, and replace. A custom report is defined entirely by config keys: `report.<name>.columns`, `report.<name>.sort`, `report.<name>.filter`, and `report.<name>.labels` (with an optional `report.<name>.description`). Once defined, the report’s name becomes a command (`task review`). ([Taskwarrior](https://taskwarrior.org/docs/report/?utm_source=chatgpt.com "Reports"))

The first thing to learn is _what you’re looking at_. Run `task columns` to see every available column (and the formats each supports). Dates, for example, can display as ISO timestamps, as “ages,” or as countdowns; you pick the format by suffixing the column in the report (e.g., `due.iso`, `scheduled.countdown`). This is how the terse, information-dense reports you’ve seen are built. ([Taskwarrior](https://taskwarrior.org/docs/commands/columns/?utm_source=chatgpt.com "Columns Command"))

A small but important detail: the column `description` shows the description _and_ annotations indented beneath it, while `description.count` shows the description plus an annotation **count** only. If your list looks “noisy,” it’s probably using `description`—switch it to `description.count` for calmer output. This behavior is documented in the `list` report page and is why many people keep the full notes for `info`/`long`, but show counts in everyday lists. ([Taskwarrior](https://taskwarrior.org/docs/commands/list/?utm_source=chatgpt.com "List Report"))

Sorting is equally flexible. A report’s sort order is a comma-separated list of _column IDs_ with a direction: `+` means ascending, `-` means descending. You can also add a forward slash `/` after the direction to insert a **blank-line break** whenever that key changes—handy for visual grouping by project. Crucially, a sort key does not have to be one of the visible columns. Here’s the syntax exactly as the manual describes it (note the grouping indicator in `project+/`): `report.list.sort=due+,priority-,start.active-,project+/`. ([Ubuntu Manpage](https://manpages.ubuntu.com/manpages/xenial/man5/taskrc.5.html?utm_source=chatgpt.com "taskrc - Configuration details for the task(1) command"))

You don’t need to touch the config to _try_ a different sort. Use a one-off override: `task rc.report.next.sort=due-,urgency- next` temporarily sorts the `next` report by due date (latest first) and then by urgency. When you close the command, your permanent settings are unchanged. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"))

It helps to remember what “next” is doing by default: `task` (with no subcommand) runs the **next** report, which is sorted by **decreasing urgency**. If urgency reflects your priorities, the tasks you should start next float to the top; if not, you can change the report’s sort or the urgency weights. ([Taskwarrior](https://taskwarrior.org/docs/best-practices/?utm_source=chatgpt.com "Best Practices"))

Now let’s make this practical with three workflows: read better, customize columns, and install a durable report you can rely on every day.

**Read better—peek under the hood quickly.** To discover how a report is built, ask Taskwarrior for its recipe: `task show report.next.columns` shows the exact column list for `next`. If you only want to _try_ a different layout once, override at runtime: `task rc.report.list.columns='id,project,priority,due,description.count,urgency' list`. If your terminal is narrow (or you’re piping to a pager) and the columns wrap badly, increase the width on the fly: `task rc.defaultwidth=150 list`. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"), [huge-man-linux.net](https://www.huge-man-linux.net/man5/taskrc.html?utm_source=chatgpt.com "taskrc - Configuration file for the task(1) command - Linux"))

**Customize columns—the right fields, the right formats.** Suppose you want due dates as countdowns, scheduled dates as relative times, and annotation counts instead of full notes. You can set that in one line for a test drive, then bake it in if you like it:

```bash
# ad-hoc
task rc.report.list.columns='id,project,tags,scheduled.countdown,due.countdown,description.count,urgency' list
```

When satisfied, make it permanent by writing the same recipe with `task config`:

```bash
task config report.list.columns  'id,project,tags,scheduled.countdown,due.countdown,description.count,urgency'
task config report.list.labels   'ID,Project,Tags,Sched,Due,Description,Urg'
```

Those keys are the canonical way to control a report’s layout and headings. If you ever wonder what columns exist or which formats they accept, run `task columns`. ([Taskwarrior](https://taskwarrior.org/docs/report/?utm_source=chatgpt.com "Reports"))

**Create a durable named report—your daily “review.”** Here’s a compact, opinionated report that groups by project, sorts by soonest due then urgency, and only shows pending tasks. Because `project+/` has the slash, you’ll get a blank line between projects:

```bash
# one-time setup
task config report.review.description 'Daily review: pending, grouped by project, soonest first'
task config report.review.columns     'id,project,priority,scheduled.countdown,due.countdown,description.count,urgency'
task config report.review.labels      'ID,Project,Pri,Sched,Due,Description,Urg'
task config report.review.filter      'status:pending'
task config report.review.sort        'project+/,due+,urgency-'

# use it any time
task review
```

If you prefer this report to ignore your active **context**, set `report.review.context=0`; by default, reports do respect whatever context is on (`task context school`). ([Debian Manpages](https://manpages.debian.org/testing/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior — Debian testing"))

Two final quality-of-life tips. First, many built-in reports hide columns that are completely empty across the current result set; that’s `print.empty.columns=off` and it keeps output narrow. If you wonder “where did that column go?”, it will appear as soon as at least one task has a value. Second, when you need to _see everything_, the `long` report prints all attributes for matching tasks—great for audits—while your everyday reports stay concise. ([Taskwarrior](https://taskwarrior.org/docs/commands/list/?utm_source=chatgpt.com "List Report"), [Ubuntu Manpage](https://manpages.ubuntu.com/manpages/xenial/man5/taskrc.5.html?utm_source=chatgpt.com "taskrc - Configuration details for the task(1) command"))

With these pieces—columns you understand, sort keys that match how you decide, and named reports you can invoke without re-typing filters—you’ll stop “printing lists” and start curating views. That’s the difference between scrolling and _seeing_.



# 9. Color, themes, and rules — make the important stuff pop

Taskwarrior’s color system is both simple and deep: you can turn colors on, pick a theme, and then layer precise rules so projects, tags, due states, priorities, and even keywords stand out at a glance.

**Quick sanity check.** First, prove color works and see your active palette:

```bash
task color
task color legend
```

If you’re piping or redirecting output (where Taskwarrior drops color by default), you can force it back for a one-off run like this:

```bash
task rc._forcecolor:on rc.defaultwidth:120 rc.detection:off next
```

These are the official diagnostics; use them any time you’re unsure whether a rule is matching. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"))

## Pick a theme (optional but nice)

Themes are just config files with predefined color rules. Uncomment or add an `include` line in `~/.taskrc`:

```bash
# ~/.taskrc
include /usr/share/task/solarized-dark-256.theme   # or dark-256.theme, etc.
```

You can always override theme colors afterward in the same file. ([Taskwarrior](https://taskwarrior.org/docs/themes/?utm_source=chatgpt.com "Color Themes"))

## Write practical color rules

Color rules are simple “when this condition matches, use that color” settings. You can add them permanently:

```bash
# Global on/off (usually on by default)
task config color on

# Tag- and project-specific
task config color.tag.next yellow
task config color.project.errands "white on blue"

# Priority colors (modern Taskwarrior uses the priority UDA)
task config color.uda.priority.H color255
task config color.uda.priority.M color250
task config color.uda.priority.L color245

# Due-state colors
task config color.due.today "bold magenta"
task config color.due "bold yellow"
task config color.overdue "bold red"

# Active task highlight
task config color.active "bold white on blue"

# Description keyword match (substring)
task config color.keyword.clean "bold yellow"
```

The `color.tag.*`, `color.project.*`, `color.keyword.*`, and `color.uda.priority.*` forms are straight from the docs. The priority mapping shown above reflects the current UDA-based priority system; themes distributed with Taskwarrior have been updated accordingly. ([manpages.debian.org](https://manpages.debian.org/unstable/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior — Debian unstable"), [Taskwarrior](https://taskwarrior.org/docs/priority/?utm_source=chatgpt.com "Priority"))

You can also test rules without saving them by injecting temporary overrides:

```bash
# Try a blue background for project:party and bold for “invitations”
task "rc.color.project.party=on rgb001" rc.color.keyword.invit=bold list
```

This trick is great for quick “does this look right?” experiments. ([manpages.debian.org](https://manpages.debian.org/jessie/taskwarrior/task-tutorial.5?utm_source=chatgpt.com "task-tutorial(5) — taskwarrior — Debian jessie"))

## Who wins? Precedence & blending

Multiple rules can match a single task (for example, a +next task in a project that’s due today). Taskwarrior resolves that with a precedence list and, by default, _merges_ foreground/background from compatible rules.

Check (and if you need, edit) your precedence:

```bash
task show rule.precedence.color
# to change:
task config rule.precedence.color "due.today,active,blocked,overdue,due,keyword.,project.,tag.,recurring,uda"
```

If your color combinations clash, you can turn off blending:

```bash
task config rule.color.merge 0
```

Both the precedence list and the merge toggle are documented; if colors look “off,” verify your precedence and try disabling merging. ([Linux Documentation](https://linux.die.net/man/5/task-color?utm_source=chatgpt.com "task-color (5) - Linux man page"), [manpages.debian.org](https://manpages.debian.org/unstable/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior — Debian unstable"))

## Make long lists readable

Alternate row shading helps when descriptions wrap to multiple lines:

```bash
# Light alternate row tint
task config color.alternate "on rgb253"

# Turn it off later
task config color.alternate ''
```

You can confirm this setting (and others) with `task show color.alternate`. ([manpages.debian.org](https://manpages.debian.org/unstable/taskwarrior/taskrc.5.en.html?utm_source=chatgpt.com "taskrc(5) — taskwarrior — Debian unstable"))

## A couple of niceties

If a particular task should ignore _all_ color rules, give it the special `+nocolor` tag:

```bash
task 42 modify +nocolor
```

Special tags alter behavior per task; `+nocolor` simply disables colorization for that item. ([Taskwarrior](https://taskwarrior.org/docs/terminology/?utm_source=chatgpt.com "Taskwarrior - Terminology"))

And when in doubt about color names, 16-color, or 256-color RGB triples, use the built-in sampler:

```bash
task color white on red
task color rgb150 on rgb020
```

These show exactly what your terminal can render. ([manpages.debian.org](https://manpages.debian.org/jessie/taskwarrior/task-tutorial.5?utm_source=chatgpt.com "task-tutorial(5) — taskwarrior — Debian jessie"))

## Does Taskwarrior-TUI respect these?

Yes—`taskwarrior-tui` reads the same `~/.taskrc` color keys (for example, `color.active`), so your core Taskwarrior rules generally carry over. If something looks different, check the TUI’s docs, but start with your `taskrc`. ([kdheepak](https://kdheepak.com/taskwarrior-tui/configuration/colors/?utm_source=chatgpt.com "Color configuration - taskwarrior-tui - A terminal user interface for ..."))

---

### Example: a minimal “urgent-first” look you can paste

```bash
# Theme base
task config color on
echo 'include /usr/share/task/dark-256.theme' >> ~/.taskrc

# Make urgency cues pop
task config color.overdue         "bold white on red"
task config color.due.today       "bold magenta"
task config color.active          "bold white on blue"

# Spotlight priority & your “next” focus tag
task config color.uda.priority.H  color255
task config color.tag.next        yellow

# Quietly style a project and a keyword you care about
task config color.project.errands "white on blue"
task config color.keyword.clean   "bold yellow"

# Keep things tidy
task config rule.color.merge 1
task config color.alternate "on rgb253"

# Sanity check
task color legend
```

If anything doesn’t appear as expected, run `task color legend` and `task show rule.precedence.color` to see what’s being applied and in what order. ([Taskwarrior](https://taskwarrior.org/docs/examples/?utm_source=chatgpt.com "Usage Examples"), [Linux Documentation](https://linux.die.net/man/5/task-color?utm_source=chatgpt.com "task-color (5) - Linux man page"))

Want me to tailor a theme around your actual projects and tags (+school, +errands, etc.) and output the exact `task config …` lines?



# 10. Visual dashboards — calendar, burndown, and history

Taskwarrior isn’t just rows and columns. It ships with a compact calendar and a few ASCII charts that help you _scan time_ and _see trendlines_.

## Calendar — due & scheduled at a glance

`task calendar` renders three months (previous, current, next) with a legend for **today**, **due**, **due:today**, **overdue**, **weekend**, and (optionally) **holidays**. A list of matching tasks prints below the calendar.

Run it plain, or add filters exactly like any other report:

```bash
# Everything due/scheduled, three-month view
task calendar

# Make the week start on Monday (persists)
task config weekstart Monday
```

**Holidays.** If you include a holiday file (many distros ship locale files, and you can also write your own), holidays show up shaded on the calendar and listed beneath it:

```bash
# In ~/.taskrc (adjust the path/locale to what you have installed)
include /usr/share/task/holidays.en-US.rc
```

**Color.** The calendar uses your color rules (from our previous section). Overdue/due-today will inherit `color.overdue` and `color.due.today`, so you can make those days pop.

## Burndown — are you burning down the backlog?

`task burndown` draws stacked bars of **Pending / Started / Done** over time and projects an **Estimated completion** from your **Net fix rate** (done minus added). You can change the granularity:

```bash
# Daily/weekly/monthly
task burndown.daily
task burndown.weekly
task burndown.monthly

# Per-project burn (weekly) — great for sprints
task project:errands burndown.weekly

# Study sprint burn: only tasks tagged +school and due this semester
task +school due:2025-08-01..2025-12-31 burndown.weekly
```

Reading it: gray bars are the open pile (Pending), a lighter slice can show Started, and blue bars are Done. If the gray area shrinks week-over-week and the projection line marches left, you’re winning.

**Tip.** If bars get squished in a narrow terminal, widen on the fly:

```bash
task rc.defaultwidth:120 burndown.weekly
```

## History & GHistory — throughput over time

Where burndown tracks the _stock_ of work, history shows _flow_ (how much you add/complete per period):

```bash
# Tabular throughput by month
task history

# Graphical history (ASCII bar chart)
task ghistory
```

Filter it the same way:

```bash
# Only school tasks in the last quarter
task +school entry.after:now-90days ghistory
```

## Practical examples you can drop into your setup

```bash
# Make “what’s coming” obvious when you open Taskwarrior
task next
task calendar

# Weekly planning ritual for your thesis work
task project:thesis burndown.weekly

# Show only the next 30 days on the calendar (and color due-today loud)
task config color.due.today "bold magenta"
```




# 9. Urgency and prioritization — How the urgency score is computed, how priority interacts with due dates/tags, and tuning weights to match your style.
    
# 9. Dependencies and blockers — Modeling workflows where one task unlocks another; visualizing and unblocking efficiently.
    
# 10. Contexts and on-the-fly configuration — Swapping entire working views with `context` and using `rc.<key>=<value>` overrides per command.
    
# 11. User-Defined Attributes (UDAs) — Adding your own fields (e.g., `effort`, `area`, `paper_id`) and integrating them into reports and filters.
    
# 12. Recurring tasks that don’t fight you — Crafting recurrences, handling exceptions, and avoiding common “recurrence storms.”
    
# 13. Hooks, templates, and bulk edits — Pre/post-command automation, task templates, CSV/JSON flows, and safe mass changes.
    
# 14. Syncing and backups — Options from Taskserver to simple file sync; key management, conflict resolution, and belt-and-suspenders backups.
    
# 15. Interop: JSON in/out and scripting — Treating Taskwarrior as a data engine: `task export|import`, stable schemas, and scripting with Bash/Python.
    
# 16. Timewarrior integration — Tracking actual time, linking intervals to tasks, and reporting where your week really went.
    
# 17. TUI/front-ends and shell comfort — Taskwarrior-TUI, Zsh/Bash completion, aliases/functions, and prompt/statusline tricks.
    
# 18. Automation on macOS — Launchd jobs for reviews, reminders, and report exports; Shortcuts/AppleScript ideas that play nicely with the CLI.
    
# 19. Opinionated workflows — GTD weekly review, research project pipelines, kanban-ish patterns, and a minimal “paper deadline” playbook.
    
# 20. Large-scale performance and hygiene — Keeping thousands of tasks snappy, archival strategies, and data-integrity checks.
    
# 21. Troubleshooting and recovery — Reading diagnostics, undo/repair tactics, migrating data, and common foot-guns to avoid.
    
# 22. Version notes: v2 vs v3 differences — What’s changed at a high level and how to choose settings that future-proof your setup.
    
# 23. Integrations you’ll actually use — Obsidian/Neovim note links, calendar hand-offs, email capture, and lightweight ChatGPT/AI helpers.
    
# 24. Security and privacy — What lives on disk, what syncs, how to keep secrets out of tasks, and safe redaction practices.
    
# 25. Appendix A: Command and attribute cheat sheet — A dense, printable index of verbs, modifiers, and attribute names.
    
# 26. Appendix B: Sample `~/.taskrc` — A sane baseline config with comments you can drop in and tune.
    