# Configuration

`bin/.cmds`, `bin/.smart`, and `bin/.conf` sit next to `bin/MonSMART.exe`. They are plain text. `#` and `;` start comments. Values are trimmed.

## Commands

`bin/.cmds` defines one section per command:

```ini
[smartctl]
smartctl -x /dev/sda
```

Only `smartctl` is accepted as the executable. Argument strings are limited to letters, digits, spaces, `.`, `-`, `:`, `,`, and `/`. Mutating flags such as `-t` and `-s` are rejected. The executable is resolved through `PATH`; if it is not found, the entry is skipped.

The section name and the last path or device component form the output key. For `smartctl -x /dev/sda`, the key is `smartctl_sda`.

## SMART attributes

`bin/.smart` maps IDs to names:

```ini
5=Reallocated Sectors
197=Current Pending Sectors
198=Offline Uncorrectable
196=Reallocation Events
199=UDMA CRC Errors

169=Endurance Remaining
194=Temperature Celsius
9=Power On Hours
```

The first five entries are important. They appear in Critical Health and trigger a warning when they change. The rest are extras. They are shown in the Extra view but never trigger a warning.

## Behavior

`bin/.conf` controls the run:

| Key | Type | Default | Description |
|---|---|---|---|
| `warnOnly` | bool | `false` | `true` shows the popup only when something changed. `false` shows it after every run. |
| `logRetention` | int | `5` | Keep the N latest timestamped run folders. Minimum 1. |
| `saveChanges` | bool | `false` | Append important changes to `logs/changelog.txt`. |
| `commandTimeoutMinutes` | int | `60` | Kill a command that runs longer than N minutes. Minimum 1. |

Booleans are case-insensitive. Only `true` enables a flag; any other value, including `false`, `1`, and `0`, is treated as false. Missing keys use the defaults above.

The tracked `bin/.conf` overrides two defaults: `logRetention=365` and `saveChanges=true`. `warnOnly` stays `false`, so the sample shows a popup after every run.

## Example

```ini
warnOnly=true
logRetention=30
saveChanges=true
commandTimeoutMinutes=60
```

This keeps 30 runs, appends important changes to the changelog, and stays quiet on healthy runs.

## Not supported

- Automatic drive discovery.
- Installing `smartctl` or adding it to `PATH`.
- Per-attribute thresholds.
- A daemon mode or built-in scheduler.
- Repair, self-test, or any command that changes the drive. MonSMART is intended for read-only checks.
