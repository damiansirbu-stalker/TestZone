TestZone: Callback monitoring and profiling for STALKER Anomaly, by Damian
GitHub: https://github.com/damiansirbu-stalker/TestZone
Changelog: https://github.com/damiansirbu-stalker/TestZone/blob/main/doc/changelog

Features:
Watcher - monitors all 162 engine callbacks with fire counts and payload logging
Deep introspection - extracts detailed info from tables and userdata arguments
Rate limiting - prevents log spam from high-frequency callbacks
Per-callback toggles - enable/disable individual callbacks via MCM
Periodic statistics - logs top-firing callbacks at configurable intervals

Requirements:
Anomaly 1.5.3
xlibs (https://www.moddb.com/mods/stalker-anomaly/addons/xlibs-1001)
MCM

Install (MO2):
1. Install xlibs (load first)
2. Install TestZone
3. Load order does not matter
4. Configure via MCM (Options -> TestZone)

Uninstall (MO2):
Disable or remove in MO2.

Configuration:
Main tab:
  Watcher enabled - master toggle for callback logging
  Periodic statistics - log callback fire counts at regular intervals
  Stats interval - how often to log periodic stats (10-3600 seconds)
  Log payload - log callback arguments (can create large log files)
  Deep introspection - extract detailed info from objects
  Table depth - recursion depth for table introspection (1-4)
  Rate limiting - throttle repeated callback logs
  Rate limit interval - min ms between same callback logs (100-10000)
Tracked Events tab:
  Per-callback enable/disable toggles for all 162 callbacks

Log output: appdata/logs/testzone.log

Performance:
Performance comes first, ahead of any feature. When a feature cannot fit the budget it is reworked, replaced, or removed with an X-Ray engine modification rather than allowed to slow the game. Measured on the engine built from the latest source with no multithreading and no optimizations, so the timings are worst-case; the optimized multithreaded build you run is always faster.

Compatibility:
Requires xlibs.
Runs on themrdemonized modded exes 2025.9.10 or newer, or AOEngine v0.55 or newer.
The full feature set needs the latest demonized build. A feature that needs a newer build stays inactive on older exes.
No known incompatibilities.

FAQ:
Do I need modded exes?
  Yes. TestZone needs themrdemonized modded exes (2025.9.10 or newer) or AOEngine (v0.55 or newer). Vanilla Anomaly does not expose the APIs it relies on.

Credits:
Altogolik - support, ideas, source materials

Development:
Written against X-Ray Monolith engine source, Demonized exes source code, and Anomaly 1.5.3 unpacked gamedata.
Code patterns and engine usage validated against established work by reputable GAMMA modders (Demonized, Vintar0, RavenAscendant, xcvb).
The code is validated in real time by a multi-stage pipeline: luacheck, selene, tree-sitter AST analysis, contract rules, cross-file dependency resolution, cyclomatic complexity analysis, crash and vulnerability pattern detection, lua54 integration testing with X-Ray engine stubs, gitleaks secret scanning.
Full report in doc/test-report.log.

Usage and License:
  Modpacks: allowed and encouraged. Keep the readme and license files.
  Addons, patches, integrations: allowed. Credit "TestZone by Damian Sirbu" visibly on your mod page.
  Reproducing the implementation in other software: not allowed, even with credit.
  Full license in LICENSE file and on GitHub.

Reporting issues and suggestions
Open a report at https://github.com/damiansirbu-stalker/TestZone/issues/new/choose, or ask on the GAMMA, EFP, Anomaly, and Zona Discord servers. Read this readme and the MCM options first.

Include: exact repro steps (new game or named save, expected vs actual), engine build, modlist, load order, xray.log, and the mod debug log. With hundreds of mods loaded, only the log shows whether this one was involved.

The debug log is required: set the MCM log level to DEBUG, reproduce, then back to WARN. DEBUG is not free. It writes a timed line for every evaluation and hitches single-threaded exes, and the millisecond figures include the tracing itself, so treat them as relative.
