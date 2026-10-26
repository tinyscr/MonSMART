# Why MonSMART exists

This is the project history. For setup and usage, start with the [README](../README.md).

## The problem

A failing SSD produced a SMART warning, but a single report only says something is wrong. It does not say whether the drive is getting worse. Manually diffing `smartctl -x` output every day does not scale.

## The approach

MonSMART started as `diskwatch`, a small winkit utility for checking drive health. The failure changed the requirement: keep history, compare each run with the previous baseline, and surface only meaningful changes. A failed status tells you there is a problem. A comparison tells you whether it is getting worse.

The tool is designed to be read-only. It collects, preserves, and highlights. It does not repair, test, or modify the drive in its default configuration.

## The rename

The project moved out of winkit into its own TinyScripts repository. The old `diskwatch` name described one job. `MonSMART` describes the monitor. The internal names, executable, and docs now use MonSMART.
