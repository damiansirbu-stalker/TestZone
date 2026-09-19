TestZone: Callback monitoring and profiling for STALKER Anomaly, by Damian
Version: 1.0.4-snapshot (xlibs 1.5.1, demonized 20250908)
Changelog: https://github.com/damiansirbu-stalker/TestZone/blob/main/doc/changelog

My work:
GitHub: https://github.com/orgs/damiansirbu-stalker/repositories
ModDB: https://www.moddb.com/members/damian-sirbu/addons
Nexus: https://www.nexusmods.com/profile/damiansirbu/mods

My contributions:
X-Ray Monolith: https://github.com/themrdemonized/xray-monolith

Features:
Watcher - monitors all 162 engine callbacks with fire counts and payload logging
Deep introspection - extracts detailed info from tables and userdata arguments
Rate limiting - prevents log spam from high-frequency callbacks
Per-callback toggles - enable/disable individual callbacks via MCM
Periodic statistics - logs top-firing callbacks at configurable intervals

Requirements:
Anomaly 1.5.3
Modded exes: themrdemonized 20250908 or newer, or AOEngine v0.55 or newer. The full feature set needs the latest demonized build. A feature that needs a newer one stays inactive on older exes.
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
Coexists with everything; no known incompatibilities.

How It's Built:

Although it started from work by Demonized, Alundaio, and Tronex, the current code and patterns are original, learned by reverse-engineering X-Ray's callback registry.
It taps all 162 engine callbacks through one interception seam, counting fires and logging payloads with deep table and userdata introspection.
Rate limiters keep a high-frequency callback from flooding the log, and each callback toggles on its own through MCM.
The design favors the engine's own mechanisms and minimal intervention. It reads the callback bus and never drives it.
Profiled continuously with JitProfiler, an engine-native scientific tool. Manual tests run on unoptimized, single-threaded exes.
Every commit runs the full pipeline locally and in CI: luacheck, a Selene build compiled for STALKER with flags the public build lacks, and a load test that runs every script against engine stubs.
Rule layers then check crash safety, hotpath cost, engine correctness, complexity, architecture contracts, security, and the docs.
It depends on no other mod, not even my own. The only shared layers are X-Ray and xlibs.

[Screenshot: TestZone under JitProfiler, a live CPU and allocation capture]
Project Health: https://damiansirbu-stalker.github.io/TestZone/

Credits:
Altogolik - support, ideas, source materials

Usage and License:
  Modpacks: allowed and encouraged. Keep the readme and license files.
  Addons, patches, integrations: allowed. Credit "TestZone by Damian Sirbu" visibly on your mod page.
  Reproducing the implementation in other software: not allowed, even with credit.
  Full license in LICENSE file and on GitHub.

Diagnostics and reporting:
The Watcher is the diagnostic surface: enable it on the Main tab and it logs callback fire counts and payloads to appdata/logs/testzone.log.
Report at https://github.com/damiansirbu-stalker/TestZone/issues/new/choose or the EFP, Anomaly, and Zona Discord. Include repro steps, engine build, modlist, load order, xray.log, and the debug log.

Tags: engine-native, performance, save-safe, debugging, engine-tracer, callbacks, introspection, monitoring, observation-only, reverse-engineering
