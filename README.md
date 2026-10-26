# MonSMART

Read-only SMART monitor that compares disk health over time and warns when something changes.

Periodic monitor, not a live dashboard or a repair tool.

## Quick start

1. Download a prebuilt binary from [Releases](https://github.com/tinyscr/MonSMART/releases), or build `bin/MonSMART.exe` with `build.ps1`.
2. Install `smartctl` from [smartmontools](https://www.smartmontools.org/) and make sure it is on `PATH`.
3. Review `bin/.cmds`, `bin/.smart`, and `bin/.conf`.
4. Run `bin/MonSMART.exe` as administrator. It prints a verdict and shows a popup.
5. Schedule the exe daily or weekly with Task Scheduler.

## What it does

- Runs the configured `smartctl` checks.
- Saves each run under `logs/runs/<timestamp>/`.
- Compares the new result with the newest readable baseline.
- Exits 0 when nothing important changed.
- Exits 1 and shows a warning popup when health, endurance, an important attribute, or the device list changed.
- With `warnOnly=true`, healthy runs show no popup. By default, a summary popup appears after every run.

## Requirements

- Windows 7 or later with .NET Framework 4.0 or later.
- `smartctl` from smartmontools.
- Administrator rights for full SMART data.
- Keep `bin` and `logs` as sibling folders.

## Limitations

- Windows-only: built on WinForms and Windows APIs.
- `smartctl` is not bundled; configure it in `bin/.cmds`.
- No drive discovery; add every device to `bin/.cmds` and its attributes to `bin/.smart`.
- No daemon mode; use Task Scheduler.
- Locale-sensitive parsing; the parser expects English `smartctl` output.
- Read-only is enforced, not guaranteed; a user can still edit `.cmds` to add repair commands.

## Docs

- [Usage](docs/usage.md)
- [Configuration](docs/config.md)
- [Troubleshooting](docs/troubleshooting.md)
- [How it works](docs/how-it-works.md)
- [Project history](docs/STORY.md)
