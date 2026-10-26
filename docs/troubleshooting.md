# Troubleshooting

## No valid commands configured

`bin/.cmds` has no usable entry.

- Check the section format: `[smartctl]` followed by the command on the next line.
- Confirm `smartctl.exe` is on `PATH`.
- Remove mutating flags such as `-t` and `-s`; MonSMART rejects them.
- Keep arguments to letters, digits, spaces, `.`, `-`, `:`, `,`, and `/`.

## Warning: no command produced output

The run directory has no `smartctl_*.json` file.

- Run `smartctl -x /dev/sda` in an elevated terminal.
- Check the device path in `bin/.cmds`.
- Check the raw output under `logs/runs/<timestamp>/` for a timeout or error.

## No report found. Run MonSMART first

The popup could not find a result under `logs/runs/`.

- Run `bin/MonSMART.exe` once.
- Keep `bin` and `logs` as sibling folders.
- Check that the run wrote `logs/runs/<timestamp>/result.json`.

## First run reports no changes

Expected. The first run has no previous baseline. The next run compares against it.

## Popup appears when nothing changed

`bin/.conf` has `warnOnly=false`. Set `warnOnly=true` to show the popup only when something important changed.

## Popup does not appear when nothing changed

`warnOnly=true` is working as intended. Check the exit code: `0` means no important changes.

## Another instance is already running

A process already holds `Global\MonSMART`. Wait for it to finish. A second launch exits with code 3.

## Admin rights

Run MonSMART as administrator. Without elevation, `smartctl` can return limited data.

## Task Scheduler runs but nothing happens

- Set the action to the full path of `bin\MonSMART.exe`.
- Enable "Run with highest privileges".
- Check Last Run Result: `0` success, `1` changes, `2` configuration error, `3` another instance.
- Confirm the task account can see `smartctl` on `PATH`.
- Keep `bin` and `logs` as sibling folders.

## Log grows forever

`logRetention` prunes timestamped run folders. `logs/changelog.txt` has no retention. Archive or delete it yourself.

## Attributes are missing

The parser expects English `smartctl` output. Other locales can cause attributes to be skipped. Raw output is preserved in `logs/runs/<timestamp>/smartctl_*.json`.

## Drive disappeared

A previously present SMART device missing from the current run counts as an important change. Check the device path and connection.

## Popup cannot be shown

A headless or non-interactive session has no desktop for the dialog. The run still writes its files. Check the exit code and the console warning.
