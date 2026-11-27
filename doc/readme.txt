Version: 1.0.4-snapshot (xlibs 1.10.1, demonized latest)
Changelog: https://github.com/damiansirbu-stalker/TestZone/blob/main/doc/changelog
Health: https://damiansirbu-stalker.github.io/TestZone/health/
JitProfiler: https://damiansirbu-stalker.github.io/TestZone/jitprofiler/
Bugs: https://github.com/damiansirbu-stalker/TestZone/issues
Russian / На русском: https://github.com/damiansirbu-stalker/TestZone/blob/main/doc/readme_ru.txt

---
Alife mods:
  AlifePlus: https://www.moddb.com/mods/stalker-anomaly/addons/alifeplus-v1-0-01
  AlifeTactics: https://www.moddb.com/mods/stalker-anomaly/addons/alifetactics
  AlifeBalance: https://www.moddb.com/mods/stalker-anomaly/addons/alifebalance
  AlifeGuard: https://www.moddb.com/mods/stalker-anomaly/addons/alifeguard-1001
Diegetic mods:
  DiegeticControl: https://www.moddb.com/mods/stalker-anomaly/addons/diegeticcontrol
  DiegeticAmbience
  DiegeticDread
Tools:
  JitProfiler: https://www.moddb.com/mods/stalker-anomaly/addons/jitprofiler
Libraries:
  xlibs: https://www.moddb.com/mods/stalker-anomaly/addons/xlibs-1001
Engines:
  X-Ray Monolith: https://github.com/themrdemonized/xray-monolith/pulls?q=is%3Apr+author%3Adamiansirbu+is%3Amerged
  OpenXRay: https://github.com/OpenXRay/xray-16/pulls?q=is%3Apr+author%3Adamiansirbu+is%3Amerged
  IX-Ray: https://github.com/ixray-team/ixray-1.6-stcop/commits/develop?author=damiansirbu
Dependents:
  COMPASS: https://github.com/Crimento/COMPASS
  Nemesis Spawn System: https://github.com/SirEndet/Nemesis-Spawn-System
  Word of Mouth: https://www.moddb.com/mods/stalker-anomaly/addons/word-of-mouth
Integrations:
  Warfare (erepb): https://www.moddb.com/mods/stalker-anomaly/addons/warfare-alife-overhaul-new
  Homestead: https://www.moddb.com/mods/stalker-anomaly/addons/homestead-stalker-settlement-builder
  Stealth Overhaul: https://github.com/Alex-leon1594/Stealth_Overhaul_Reworked
  TALKER: https://www.moddb.com/mods/stalker-anomaly/addons/talker-ai-powered-npc-dialogue-beta
  Factions of the Zone: https://github.com/devinhorowitz/factionsofthezone
  Anomaly Radios Extended: https://www.moddb.com/mods/stalker-anomaly/addons/anomaly-radios-extended-full-remaster-152
Collaborations:
  xAGNA: https://www.moddb.com/mods/stalker-anomaly/addons/xagna

My work is free and open source, ships in most modpacks, and integrates with established projects.
An organized group copies my work, defames, impersonates and review-bombs me across platforms.
You can help with a review, a rating, a proper bug report, or a coffee on Ko-fi: https://ko-fi.com/damiansirbu

Features:

Main:
  Watcher - logs every callback your game version defines with fire counts, and the payload of each on request
  High-rate callbacks - adds the callbacks that fire every frame or every shot to the watcher
  Deep introspection - reads type, name, section and community out of tables and engine objects
  Rate limiting - holds back a callback that fires faster than you can read it
  Periodic statistics - logs the top-firing callbacks at a set interval
  Mutant scan - mutant squads with species, faction, online state, smart terrain and commander
  Level scan - the squads, smart terrains, stashes and AlifePlus squads on the current level
  World scan - a squad census by faction and by level, with online and offline counts
  Geometry scan - smart terrain distances, stash density, base spacing and feature layout per level
  Timer queue watch - names the timer that broke the queue when every timer in the game stops
Profiler:
  Times a set of suspect GAMMA functions and logs their frame cost and spikes
Perception, one switch per system, one line per change, written to testzone_perception.log:
  Hearing - every hearing event that reaches a stalker, with its power against his threshold
  Vision - when he starts or stops seeing you, and his detection progress
  Memory - his visual, sound and hit memory of you
  Danger - his held danger, the action that answers it, and how far he moved
  Enemy - who he picks as enemy and drops, and whether he heard or saw you first
  Radius, All sound sources and Include mutants set who the trace follows
GeoTool:
  A window on a key you bind, with your position and every place check live at your spot
  The checks: nearest smart terrain and base owner, the indoor, roof and underground tests, emission and shelter cover, rain and sky light
  A note saves the spot and every reading to the GeoTool file of this game run
SoundTool:
  A window on End that plays channels, dread categories, NPC voice and any .ogg on disk
  It plays a sound the way the game code that owns it does, or at the ear or a set distance, with its loudness and falloff
  An MCM Mute option silences the soundscape, weapons and music while you listen; save and load with it on to silence what was already playing
  It browses any folder and opens playlists, and every note goes to the SoundTool file of this game run
  A sound trace logs every sound the soundscape plays, with its channel, distance, map and weather
Development:
  Log level and Immediate flush for every TestZone log, and Reset to defaults
Remote:
  With -dbg, a Lua file dropped into appdata/testzone/inbox runs once and one in watch runs on every load and edit, with each run's result written to outbox
  A reload, tz_remote.load_module, swaps a script for its edited source while the game runs
  tz_remote.load_all_scripts reloads every script of every mod through a save and a load
Console tools, run through run_string in debug mode:
  tz_commands - a stalker arena and a mutant arena, a life pump that keeps you alive, an actor drive and aim, kits, spawns, weather, time, and teleports to a smart terrain or a stash
  tz_commands.start_stroll - tours every level in every weather with nobody at the keys
  tz_anim - plays a motion on the HUD, a mutant or an NPC and logs whether it played
  AnimationTool, on PgDn - plays each motion of a playlist from any folder on the stalker you aim at
  tz_fx - cycles post-process effects and ambient sound loops
  tz_loadgen - 30 load cases for checking what a JitProfiler capture caught or missed

Requirements:
Anomaly 1.5.3
Modded exes: themrdemonized 20250908 or newer, or AOEngine v0.5.2 or newer. The full feature set needs the latest demonized build. A feature needing a newer one is inactive or reduced on older exes.
xlibs (https://www.moddb.com/mods/stalker-anomaly/addons/xlibs-1001)
MCM

Logs go to appdata/logs: testzone.log, and one file each for Perception, the sound trace and the arenas.
Each tool writes one file per game run, named by the tool and the launch time, as sound_tool_20261122_162100.txt. Every line starts with the time and the playlist, pool or level it came from.
Perception reads engine state only.
A run with an AI mod on and a run with it off compare line for line.

Compatibility:
Coexists with everything. A callback tracer with no gameplay of its own, observation only.

How It's Built:

Although it started from work by Demonized, Alundaio, and Tronex, the current code and patterns are original, learned by reverse-engineering X-Ray's callback registry.
It taps every callback the game version defines through one interception seam, counting fires and logging payloads with deep table and userdata introspection.
Rate limiters keep a high-frequency callback from flooding the log.
The design favors the engine's own mechanisms and minimal intervention. It reads the callback bus and never drives it.
Profiled continuously with JitProfiler, an engine-native profiler. Manual tests run on unoptimized, single-threaded exes.
Every commit runs the full pipeline locally and in CI: luacheck, a Selene build compiled for STALKER with flags the public build lacks, and a load test that runs every script against engine stubs.
Rule layers then check crash safety, hotpath cost, engine correctness, complexity, architecture contracts, security, and the docs.
It depends on no other mod, not even the author's own. The only shared layers are X-Ray and xlibs.

That pipeline runs on every commit and publishes what it finds. The header links a live health page and a JitProfiler capture of the mod's real CPU and allocation cost.

Credits:
Altogolik provided support, ideas, and source materials.

Usage and License:
  Modpacks: allowed and encouraged. Keep the readme and license files.
  Addons, patches, integrations: allowed. Credit "TestZone by Damian Sirbu" visibly on your mod page.
  Reproducing the implementation in other software: not allowed, even with credit.
  The full license is in the LICENSE file and on GitHub.

Diagnostics and reporting:
Every release goes through careful engineering and testing, but bugs can still slip through.
To report one, reproduce with debug logging on, and the world log where the mod has one.
First rule this mod out: reproduce with it off, then on.
The cleanest test is this mod alone on vanilla and xlibs.
Send the traces to my Anomaly Discord thread: https://discord.com/channels/456765861953536020/1512539453791670402
Join the server first through the Anomaly Discord invite: https://discord.gg/c4RuJNs
Or file a defect with the same information on GitHub: https://github.com/damiansirbu-stalker/TestZone/issues
Attach xray.log, the mod log, the engine build, the modlist, and the load order.
For deep technical details and mechanisms, check the architecture docs on GitHub.

Tags: engine-native, performance, save-safe, debugging, engine-tracer, callbacks, introspection, monitoring, observation-only, reverse-engineering
