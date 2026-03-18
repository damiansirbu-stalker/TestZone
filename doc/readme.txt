TestZone: Callback monitoring and profiling for STALKER Anomaly, by Damian
Latest: 1.0.0 (xlibs 1.0.4)

Features:
Watcher - monitors all 162 engine callbacks with fire counts and payload logging
Deep introspection - extracts detailed info from tables and userdata arguments
Rate limiting - prevents log spam from high-frequency callbacks
Per-callback toggles - enable/disable individual callbacks via MCM
Periodic statistics - logs top-firing callbacks at configurable intervals

Requirements:
Anomaly 1.5.3
Modded exes
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

Compatibility:
No known incompatibilities.

Development:
Written against X-Ray Monolith engine source, Demonized exes source code,
and Anomaly 1.5.3 unpacked gamedata. Code patterns and engine usage validated against established work
by reputable GAMMA modders (Demonized, Vintar0, RavenAscendant, xcvb). The
code is validated in real time by a multi-stage pipeline: luacheck, selene,
tree-sitter AST analysis, contract rules, cross-file dependency
resolution, cyclomatic complexity analysis, crash and vulnerability pattern
detection, lua54 integration testing with X-Ray engine stubs, gitleaks
secret scanning. Full report in doc/test-report.log.

Versions:

1.0.0
  Renamed from AlifeLogger. Multi-feature test lab.
  Changed: renamed AlifeLogger -> TestZone (prefix al_ -> tz_)
  Changed: log file alife_logger.log -> testzone.log
  Changed: MCM id al -> tz
