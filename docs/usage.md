# Usage

MonSMART runs once per invocation. It is not a daemon and has no tray icon.

## Build

Skip this if you downloaded a release.

Run `build.ps1` from the repo root. It finds the .NET Framework 4.0 compiler and writes an AnyCPU `bin/MonSMART.exe`.

If PowerShell blocks the script, run:

```
powershell -NoProfile -ExecutionPolicy Bypass -File .\build.ps1
```

## First run

1. Install `smartctl` and make sure it is on `PATH`.
2. Review `bin/.cmds`, `bin/.smart`, and `bin/.conf`. See [configuration](config.md).
3. Run `bin/MonSMART.exe` as administrator.
4. Check the console verdict and the popup.

The first run has no baseline. It saves a result and reports no changes.

## Schedule

Use Task Scheduler with a daily or weekly trigger.

1. Action: start `bin\MonSMART.exe` by full path.
2. Enable "Run with highest privileges".
3. Run the task once and check `logs/runs/`.

MonSMART resolves paths from the exe location. Keep `bin` and `logs` as sibling folders.

## Output

The console prints one line per drive with health and endurance when run from a terminal. The popup shows the summary; Extra opens device identity, endurance, important attributes, and extras.

Each run writes to `logs/runs/<timestamp>/`:

- `result.json`: parsed state used for comparison.
- `smartctl_*.json`: raw command output.

`logs/changelog.txt` is appended when `saveChanges=true` and an important change is detected.

With `warnOnly=true`, a healthy run exits without a popup. With `warnOnly=false`, the summary popup appears after every run.

## Exit codes

| Code | Meaning |
|---|---|
| 0 | Healthy, no changes since the last readable run |
| 1 | Something important changed |
| 2 | No valid commands configured |
| 3 | Another instance is already running |
