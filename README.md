# TestZone: Callback monitoring and profiling for STALKER Anomaly

Watches every engine callback with fire counts and payload logging, including introspection of table and userdata arguments.
Rate limiting keeps high-frequency callbacks from flooding the log, and each callback toggles on its own through MCM.

[Releases](https://github.com/damiansirbu-stalker/TestZone/releases) | [Bugs, suggestions](https://github.com/damiansirbu-stalker/TestZone/issues)

[![ci](https://github.com/damiansirbu-stalker/TestZone/actions/workflows/ci.yml/badge.svg)](https://github.com/damiansirbu-stalker/TestZone/actions/workflows/ci.yml) [![Project Health](https://img.shields.io/badge/project_health-dashboard-00ced1)](https://damiansirbu-stalker.github.io/TestZone/)

Requires: Anomaly 1.5.3, modded exes (themrdemonized or AOEngine), [xlibs](https://www.moddb.com/mods/stalker-anomaly/addons/xlibs-1001), MCM. Exact versions in [readme.txt](doc/readme.txt).

## My work

- Alife mods: [AlifePlus](https://www.moddb.com/mods/stalker-anomaly/addons/alifeplus-v1-0-01) · [AlifeTactics](https://www.moddb.com/mods/stalker-anomaly/addons/alifetactics) · [AlifeBalance](https://www.moddb.com/mods/stalker-anomaly/addons/alifebalance) · [AlifeGuard](https://www.moddb.com/mods/stalker-anomaly/addons/alifeguard-1001)
- Diegetic mods: [DiegeticControl](https://www.moddb.com/mods/stalker-anomaly/addons/diegeticcontrol) · DiegeticAmbience · DiegeticDread
- Tools: [JitProfiler](https://www.moddb.com/mods/stalker-anomaly/addons/jitprofiler)
- Libraries: [xlibs](https://www.moddb.com/mods/stalker-anomaly/addons/xlibs-1001)
- Engines: [X-Ray Monolith](https://github.com/themrdemonized/xray-monolith/pulls?q=is%3Apr+author%3Adamiansirbu+is%3Amerged) · [OpenXRay](https://github.com/OpenXRay/xray-16/pulls?q=is%3Apr+author%3Adamiansirbu+is%3Amerged)
- Integrations: [Word of Mouth](https://github.com/joshcoppola/word_of_mouth) · [Warfare (erepb)](https://www.moddb.com/mods/stalker-anomaly/addons/warfare-alife-overhaul-new) · [Stealth Overhaul](https://github.com/Alex-leon1594/Stealth_Overhaul_Reworked) · [COMPASS](https://github.com/Crimento/COMPASS)
- Collaborations: [xAGNA](https://www.moddb.com/mods/stalker-anomaly/addons/xagna)

## Documentation

- [readme.txt](doc/readme.txt) - full description, features
- [changelog](doc/changelog) - version history

## License

PolyForm Perimeter License. See [LICENSE](LICENSE).
