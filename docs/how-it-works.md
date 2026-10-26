# How it works

MonSMART runs once, records a snapshot, compares it with the previous snapshot, and exits. It has no service or background loop.

## Run flow

1. Acquire the `Global\MonSMART` mutex. A second instance exits with code 3.
2. Load `bin/.cmds`, `bin/.smart`, and `bin/.conf` from the exe folder.
3. Run each configured command with its timeout and save the raw output under `logs/runs/<timestamp>/`.
4. Build the current state and save `result.json`.
5. Scan previous run folders newest first until a readable `result.json` is found.
6. Compare health, endurance, important attributes, and missing devices.
7. Print the console summary. Append the changelog when configured.
8. Show the popup when needed. Exit 0 for no important changes, 1 for changes.

If no valid commands are configured, MonSMART exits with code 2 before running anything.

## Checks

| Check | Command | What it reports |
|---|---|---|
| SMART | `smartctl -x /dev/sda` | Drive identity, overall health, endurance, watched attributes |

Only `smartctl` is accepted as the executable. Arguments are sanitized against shell metacharacters. Known mutating flags such as `-t`/`--test` and `-s`/`--set` are rejected, so the default configuration is read-only. A user can still edit `.cmds`.

The executable is resolved to an absolute path at startup. A per-command timeout kills hung processes and records them as timed out. Invalid entries are skipped.

## State and comparison

Every run saves parsed state to `logs/runs/<timestamp>/result.json`. Previous run directories are scanned backward until a parseable baseline is found, so a corrupted intermediate baseline is skipped.

These differences trigger a change:

- SMART health changed.
- SMART endurance changed.
- Any important SMART attribute changed.
- A new SMART device appeared.
- A previously present SMART device disappeared.

Extra attribute changes are tracked but never trigger a warning. If previous run directories exist but none contains a readable `result.json`, that counts as an important change. If no previous run directories exist, no changes are reported.

## Raw output logging

Every run saves the raw command output to `logs/runs/<timestamp>/smartctl_*.json`, alongside `result.json`. If the parser misinterprets a tool's output, the raw output is still available for manual inspection.

Only the `logRetention` most recent timestamped run directories are kept. Older runs are pruned on each execution. Non-timestamped directories and symlinks under `logs/runs/` are never pruned.

## Changelog

When `saveChanges=true`, every run with an important change appends a timestamped entry to `logs/changelog.txt`:

```
[2026-09-06 12:55:59]
smartctl_sda: health PASSED -> FAILED
```

Normal healthy runs append nothing. Extra-attribute-only diffs never qualify. The file is append-only and has no retention.

## Popup

At the end of the run, a custom dark-themed dialog with Consolas monospace font shows the summary. With `warnOnly=true`, it appears only when something important changed. With `warnOnly=false`, it appears after every run.

The Extra button opens the full SMART breakdown in the same window: device identity, endurance, the Critical Health table, and the extras table. The view is scrollable when it overflows.

## Source tree

```
MonSMART/
├── src/
│   ├── program.cs           Main(), mutex, command running
│   ├── commandRunner.cs     process launcher
│   ├── config/
│   │   ├── commands.cs      .cmds loading and validation
│   │   ├── smartattrs.cs    .smart loading
│   │   └── settings.cs      .conf loading
│   ├── models/
│   │   ├── state.cs         SmartState, MasterState
│   │   └── persistence.cs   load, save, diff, JSON mapping
│   ├── parsers/
│   │   ├── conf.cs          shared config reader
│   │   ├── build.cs         run directory to MasterState
│   │   └── smartctl.cs      smartctl parsing
│   └── popup/
│       ├── window.cs        popup dialog
│       └── scrollpanel.cs   custom scrollbar
├── bin/
│   ├── MonSMART.exe         compiled binary
│   ├── .cmds                commands to run
│   ├── .smart               SMART attribute IDs and names
│   └── .conf                behavior flags
├── docs/
│   ├── usage.md
│   ├── config.md
│   ├── troubleshooting.md
│   ├── how-it-works.md
│   └── STORY.md
├── logs/
│   └── runs/                per-run result and raw output
├── .github/                 release workflow and notes template
├── LICENSE
├── .gitignore
├── build.ps1
└── README.md
```
