

# 1. Installation


Absolutely—on macOS the easiest way to get **Timewarrior** (the official name—one word) is via Homebrew. Here’s the quickest, cleanest path, plus optional alternatives and a first-run check.

### 1) Install with Homebrew (recommended)

1. Make sure Homebrew is installed (`brew --version`). If not, install it using the official instructions (they also list prerequisites like the Xcode Command Line Tools). ([Homebrew Documentation](https://docs.brew.sh/Installation?utm_source=chatgpt.com "Installation — Homebrew Documentation"))
    
2. In Terminal, install Timewarrior:
    

```bash
brew install timewarrior
```

This installs the current stable release (as of Aug 2025, that’s 1.9.0). ([Homebrew Formulae](https://formulae.brew.sh/formula/timewarrior?utm_source=chatgpt.com "timewarrior — Homebrew Formulae"), [Timewarrior](https://timewarrior.net/?utm_source=chatgpt.com "Timewarrior"))

3. Verify:
    

```bash
timew --version
```

You should see a version string (e.g., `1.9.0`). ([Homebrew Formulae](https://formulae.brew.sh/formula/timewarrior?utm_source=chatgpt.com "timewarrior — Homebrew Formulae"))

> Tip (Apple Silicon): if `timew` isn’t found right after installing Homebrew, initialize your shell env once:
> 
> ```bash
> eval "$(/opt/homebrew/bin/brew shellenv)"
> ```
> 
> (Add that line to `~/.zprofile` to persist.) ([Homebrew Documentation](https://docs.brew.sh/Installation?utm_source=chatgpt.com "Installation — Homebrew Documentation"))

---

### 2) First run & where files live

Just run `timew` once and accept the prompts. Timewarrior will create config and data in standard XDG locations:

```bash
timew
# Answer "yes" to create:
#   ~/.config/timewarrior
#   ~/.local/share/timewarrior
```

No other setup is required. ([Timewarrior](https://timewarrior.net/tutorial/first_light/ "Timewarrior - First Light! - Timewarrior"))

---

### 3) Quick smoke test

```bash
timew start writing +thesis
sleep 3
timew stop
timew summary
```

You should see a short interval reported.

---

### 4) Optional: integrate with Taskwarrior

If you use Taskwarrior, you can have tasks auto-start/stop tracking in Timewarrior using the **on-modify** hook. Copy the hook that ships with your package into Taskwarrior’s hooks directory and make it executable:

*==The following is incorrect==*

```bash
# Homebrew path (works on Intel & Apple Silicon):
cp "$(brew --prefix)"/share/doc/timew/ext/on-modify.timewarrior ~/.task/hooks/
chmod +x ~/.task/hooks/on-modify.timewarrior

# Check it’s active:
task diagnostics
```

You should see `on-modify.timewarrior (executable)` under “Hooks > Active”. From then on, `task <id> start` begins tracking, and completing/stopping a task stops tracking. ([Timewarrior](https://timewarrior.net/docs/taskwarrior/ "Timewarrior - Taskwarrior - Timewarrior"))



# Timewarrior for Taskwarrior users: a practical guide

You’ve already got a solid Taskwarrior list. Timewarrior is the stopwatch that pairs with it—recording _intervals_ of time and labeling them with _tags_. Think of your database as a stack of timestamped intervals; you can start/stop them live, add them after the fact, filter them into reports, and fix mistakes. The official docs use exactly this mental model. ([Timewarrior](https://timewarrior.net/reference/timew.1/ "- Timewarrior"))

---

## The two ways to track: from Taskwarrior or directly in Timewarrior

If you installed the `on-modify.timewarrior` hook, starting or stopping a Taskwarrior task automatically starts or stops tracking in Timewarrior (it passes the task’s description, project, and tags as Timewarrior tags). For example, `task 9 start` will begin a time interval labeled with your task’s words and project (e.g., `garden`). Use `task 9 stop` or complete the task to end the interval. You can confirm the hook path and how it works in the official integration page. ([Timewarrior](https://timewarrior.net/docs/taskwarrior/ "Timewarrior - Taskwarrior - Timewarrior"))

You can also operate Timewarrior directly:

```bash
# start a live timer with tags (note: NO leading '+' in Timewarrior)
timew start garden "remove weeds"

# stop the current timer
timew stop
```

Timewarrior’s command set—`start`, `stop`, `continue`, `track`, etc.—is documented in the syntax and man pages. ([Timewarrior](https://timewarrior.net/docs/syntax/ "Timewarrior - Syntax - Timewarrior"))

> Quick status: run `timew` with no arguments to see whether a timer is running and a small summary; it exits 0 if tracking, 1 if not. ([Timewarrior](https://timewarrior.net/reference/timew.1/ "- Timewarrior"))

---

## Seeing where your time went

The workhorse is `summary`. By default it shows **today**; you can filter by date ranges and tags.

```bash
# today’s work
timew summary

# this week’s work on garden tasks only
timew summary :week garden

# show IDs so you can edit specific intervals
timew summary :week :ids
```

Range “hints” like `:day`, `:week`, `:lastweek`, `:month`, `:year` are built in, and `:ids` adds the interval IDs (`@1`, `@2`, …) you’ll use for edits. ([Timewarrior](https://timewarrior.net/docs/summary/ "Timewarrior - timew-summary - Timewarrior"))

For a bird’s-eye chart, use day/week/month reports:

```bash
timew day
timew week
timew month
```

These draw ASCII timelines of tracked vs. untracked time; they accept the same filters. ([Timewarrior](https://timewarrior.net/reference/timew-chart.1/?utm_source=chatgpt.com "timew-day(1)"))

---

## Tracking after the fact (and backdating)

Forgot to start the clock? Add a _closed_ interval directly:

```bash
# track 10:00–11:20 for “Draft Fig. 2 methods paragraph”
timew track 10:00 - 11:20 paper "Draft Fig. 2 methods"
```

You can also backdate `start`/`stop` (e.g., “start 3 pm”), but `track` is the cleanest way to insert precise past intervals. ([Timewarrior](https://timewarrior.net/tutorial/precise/?utm_source=chatgpt.com "Precise Time Tracking"))

To resume the most recent closed interval (say you took a short break):

```bash
timew continue
```

That resumes the last thing you were doing. ([Timewarrior](https://timewarrior.net/reference/timew.1/ "- Timewarrior"))

---

## Fixing mistakes (the part you’ll use a lot)

First, if you just did the wrong thing, **undo** rolls back the previous Timewarrior command:

```bash
timew undo
```

There’s a full, documented undo journal—use `undo` repeatedly to step backward. ([Timewarrior](https://timewarrior.net/reference/timew-undo.1/?utm_source=chatgpt.com "timew-undo(1)"))

When corrections are more specific, grab an **ID** from `timew summary :ids`, then:

```bash
# change the end time
timew modify end @1 17:45

# move the start time to a new timestamp
timew move @1 2025-08-24T14:05:00

# lengthen or shorten by a duration
timew lengthen @1 10min
timew shorten  @1 5min

# split a too-long interval in two (interactive prompt)
timew split @1
```

These are the documented tools for adjusting interval bounds without rewriting the whole day. ([Timewarrior](https://timewarrior.net/reference/timew-modify.1/?utm_source=chatgpt.com "timew-modify(1)"), [Arch Manual Pages](https://man.archlinux.org/man/timew-modify.1.en?utm_source=chatgpt.com "timew-modify(1)"))

If you tracked with messy tags (common when the Taskwarrior hook forwards a long description), you can retag:

```bash
# replace all tags on @1 with exactly these
timew retag @1 paper writing

# add or remove specific tags instead
timew tag   @1 "chapter 1"
timew untag @1 "chapter 1"
```

Tag/retag/untag are first-class commands in Timewarrior. ([Timewarrior](https://timewarrior.net/reference/timew-retag.1/?utm_source=chatgpt.com "timew-retag(1)"))

Annotations—short free-text notes per interval—are also supported:

```bash
timew annotate @1 "paired t-test on RA problem set"
```

See the annotate reference for examples (an empty string removes it). ([Timewarrior](https://timewarrior.net/reference/timew-annotate.1/?utm_source=chatgpt.com "timew-annotate(1)"))

---

## Make the reports match your actual work hours (exclusions)

If you leave a timer running overnight, you don’t want 18 hours of “work.” Define your workday and weekends once in the config; Timewarrior automatically clips open intervals outside those hours:

```ini
# ~/.timewarrior/timewarrior.cfg  (XDG paths are also supported)
define exclusions:
  monday    = <9:00 12:30-13:00 >17:00
  tuesday   = <9:00 12:30-13:00 >17:00
  wednesday = <9:00 12:30-13:00 >17:00
  thursday  = <9:00 12:30-13:00 >17:00
  friday    = <9:00 12:30-13:00 >17:00
  saturday  = >0:00
  sunday    = >0:00
```

That single block captures a 9–5 schedule with a 30-minute lunch and weekends off; summaries and charts will respect it. ([Timewarrior](https://timewarrior.net/docs/workweek/ "Timewarrior - Work Week - Timewarrior"))

---

## Filtering your week by your PhD work vs. admin vs. garden

Tags are just words (or quoted phrases). There’s no `+` prefix in Timewarrior. So to see only thesis writing time for the current week:

```bash
timew summary :week paper writing
```

To focus on an admin email blitz:

```bash
timew summary :week phd admin email
```

And for the garden task you highlighted:

```bash
timew summary :week garden
```

Multiple tags narrow (logical AND), and you can always quote multi-word tags. The tags docs and the summary page cover these filters and examples. ([Timewarrior](https://timewarrior.net/docs/tags/?utm_source=chatgpt.com "timew-tags"))

---

## Exporting your data (for analysis or billing)

Everything you can filter, you can also **export** as JSON:

```bash
# export this month’s tracked time for paper writing
timew export :month paper writing > timew-paper.json
```

The export format includes start, end, tags, annotation, and ID—perfect for pandas/R. ([Timewarrior](https://timewarrior.net/reference/timew-export.1/?utm_source=chatgpt.com "timew-export(1) - Timewarrior"))

---

## Useful “extras” when you get curious

- `timew gaps :week` shows **untracked** time that isn’t excluded—great for spotting holes. ([Timewarrior](https://timewarrior.net/reference/timew-gaps.1/?utm_source=chatgpt.com "gaps"))
    
- `timew day|week|month :ids` prints charts with interval IDs so you can jump straight into edits. ([Timewarrior](https://timewarrior.net/reference/timew-chart.1/?utm_source=chatgpt.com "timew-day(1)"))
    
- `timew show` and `timew config` let you read and set config from the CLI (with `:yes` if you want non-interactive changes). ([Timewarrior](https://timewarrior.net/docs/configuration/?utm_source=chatgpt.com "Configuration"))
    

---

## A quick “from your list” walkthrough

Say you want to track work on **ID 5 “Draft Fig. 2 methods paragraph”** from Taskwarrior. With the hook installed:

```bash
# start via Taskwarrior; Timewarrior tracks automatically
task 5 start

# work a bit, then stop
task 5 stop

# check it in Timewarrior
timew summary :day :ids
```

If the interval’s tags are too verbose, replace them with something clean:

```bash
timew retag @1 paper "Fig. 2 methods"
```

If you forgot to start, add it after the fact:

```bash
timew track 14:00 - 15:10 paper "Fig. 2 methods"
```

Everything above is standard, documented behavior of the hook and core commands. ([Timewarrior](https://timewarrior.net/docs/taskwarrior/ "Timewarrior - Taskwarrior - Timewarrior"))

---

## Common pitfalls (and how to avoid them)

- **Using `+` signs for tags.** That’s Taskwarrior syntax; in Timewarrior tags are bare words or quoted phrases. ([Timewarrior](https://timewarrior.net/docs/tags/?utm_source=chatgpt.com "timew-tags"))
    
- **Overnight intervals.** Define exclusions once; they’ll clip open intervals to your workday. ([Timewarrior](https://timewarrior.net/docs/workweek/ "Timewarrior - Work Week - Timewarrior"))
    
- **Overlaps.** If you try to modify an interval into an overlap, Timewarrior errors unless you explicitly allow adjustments (e.g., with `:adjust`). Prefer fixing start/end with `modify`, or insert with `track`. ([Timewarrior](https://timewarrior.net/reference/timew-modify.1/?utm_source=chatgpt.com "timew-modify(1)"))
    

---

If you want, tell me which slices you care about most (e.g., _paper vs. admin_, _RA problem set vs. writing_), and I’ll give you a few ready-to-paste report commands tailored to your tags and schedule.