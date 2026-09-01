
<pre align="center">
 _____ ______ ____      ____ __             ____ _          __         
/ ___//_  __// __/____ / __// /___ _ ___ _ / __/(_)___  ___/ /___  ____
/ /__   / /  / _/ /___// _/ / // _ `// _ `// _/ / // _ \/ _  // -_)/ __/
\___/  /_/  /_/       /_/  /_/ \_,_/ \_, //_/  /_//_//_/\_,_/ \__//_/   
                                    /___/                              
</pre>

<p align="center">
  <b>PowerShell search tool for finding CTF flags and secrets across a Windows system — files, registry, event logs, DNS cache, recycle bin and archives.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-1.0-blue" alt="version 1.0">
  <img src="https://img.shields.io/badge/platform-Windows-blue" alt="platform Windows">
  <img src="https://img.shields.io/badge/PowerShell-5.1%2B-blue" alt="PowerShell 5.1+">
  <img src="https://img.shields.io/badge/dependencies-none-brightgreen" alt="dependencies none">
  <img src="https://img.shields.io/badge/use-CTF%20%2F%20authorized%20only-red" alt="authorized use only">
</p>

---

## Contents

- [Description](#description)
- [Search scope](#search-scope)
- [Default file types](#default-file-types)
- [Parameters](#parameters)
- [Examples](#examples)
- [Output](#output)
- [Legal and responsible use](#legal-and-responsible-use)

## Description

**CTF-FlagFinder** searches a Windows system for CTF-style flag strings
and similar secrets — in file contents and file names, the registry,
DNS cache, recycle bin, event logs, and inside ZIP and OOXML archives
(`.docx`, `.xlsx`). It runs as a single PowerShell script with no
external dependencies, and can optionally reach into hidden and
protected areas such as `AppData` and `$Recycle.Bin`.

```powershell
powershell -ep Bypass -File .\CTF-FlagFinder.ps1 -Path "C:\Users" -FlagFormat "FLAG"
```

## Search scope

| Area | What it does |
| --- | --- |
| File content & names | Scans file contents and file names, including extension-less entries such as `hosts` |
| Hidden / protected areas | `AppData`, `C:\$Recycle.Bin` and similar, with `-IncludeHidden` |
| Archives | Recurses into ZIP and OOXML files (`.docx`, `.xlsx`) |
| Registry | `HKLM` and `HKCU`, optionally every path with `-DeepReg` |
| Event logs | Application, Security and System logs |
| DNS cache | Equivalent to `ipconfig /displaydns` |
| Partitions | Detects multiple volumes; only the target path's partition is searched by default, with a notice if others exist |

By default only the file types listed below are searched — pass
`-AllFiles` to search every file type instead.

## Default file types

Used when `-AllFiles` is **not** set:

```
bak, bat, c, cfg, cpp, csv, db, go, html, ini, java, js, json, log, md,
pcap, ps1, psd1, py, rb, reg, sqlite, txt, (no extension), xml, yml, zip
```

## Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `-Path "<path>"` | string | Search root, scanned recursively. Example: `-Path "C:\Users"` |
| `-IncludeHidden` | switch | Also searches hidden files and protected directories (`AppData`, Recycle Bin) |
| `-AllFiles` | switch | Searches every file type instead of the default list above |
| `-FlagFormat "<pattern>"` | string | Custom flag pattern — a literal string (`"HTB"`, `"FLAG"`) or a full regex. Matching is case-insensitive and overrides the default pattern |
| `-DeepReg` | switch | Searches every registry path instead of just `HKLM`/`HKCU` roots. ⚠️ Can take a very long time |
| `-OutFile "<file.csv>"` | string | Writes results to the given CSV file, e.g. `-OutFile "C:\results.csv"` |

## Examples

Search the entire `C:` drive, including hidden locations, for anything
containing `RASTA`, across every file type:

```powershell
powershell -ep Bypass -File "C:\Users\Administrator\Documents\CTF-FlagFinder.ps1" `
  -Path "C:\" -IncludeHidden -AllFiles -FlagFormat "RASTA"
```

Search the `E:` drive with the default file types, including hidden
directories such as `AppData` and the Recycle Bin:

```powershell
powershell -ep Bypass -File "C:\Users\Administrator\Documents\CTF-FlagFinder.ps1" `
  -Path "E:\" -IncludeHidden
```

Custom regex pattern, results written to a CSV file:

```powershell
powershell -ep Bypass -File "C:\Users\Administrator\Documents\CTF-FlagFinder.ps1" `
  -Path "C:\CTF" -FlagFormat "[A-Za-z0-9_]{4,}\{[^}]+\}" -OutFile "C:\results.csv"
```

Full sweep — every registry path, every file type, hidden areas
included. Slow; use only when a targeted search has come up empty:

```powershell
powershell -ep Bypass -File "C:\Users\Administrator\Documents\CTF-FlagFinder.ps1" `
  -Path "C:\" -IncludeHidden -AllFiles -DeepReg -FlagFormat "FLAG"
```

`` ` `` at the end of a line is PowerShell's line-continuation
character — keep it if you split a command across lines, drop it if you
join everything onto one.

## Output

Matches print to the console as they are found. Add `-OutFile` to also
write them to a CSV file, useful for large sweeps or for keeping a
record across multiple runs.

## Legal and responsible use

This tool is built for CTF competitions, authorized penetration tests
and your own systems. It reads registry hives, event logs, the DNS
cache and hidden user data — only run it against systems you own or are
explicitly authorized to assess. Running `-DeepReg` or `-AllFiles`
against a production system without authorization is not something
this README endorses, and unauthorized use may be illegal in your
jurisdiction.
