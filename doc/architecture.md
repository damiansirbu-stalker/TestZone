# TestZone architecture

TestZone is the development bench for the mod family. It watches engine callbacks, runs scans, traces perception and sound, and hosts the review tools that curate animations, sounds and places.
The user runs it at the desk. It adds no gameplay, and it ships beside the mods it inspects.

## Glossary

- A tool is a game UI window the user opens on a key to review content and log a call or note: AnimationTool, GeoTool, SoundTool.
- A trace is a feature that logs what the game does while the user plays, with a separate MCM tab and log file: the watcher, the perception trace, the sound trace.
- A target mod is the mod a tool or trace reaches into, such as the dread mod for SoundTool.
- A device is the in-game object a tool is drawn as: one kit of vanilla textures with a case, screens, keys and lamps.
- A base layout is a tool's `configs/ui/<tool>.xml`, drawn in true 4:3 units with `anchor=` on its root.
- A variant is the `_16` or `_21` copy of a base layout that `stalker-manager.sh ui-variants TestZone` writes. X-Ray loads it on a 16:9 or 21:9 screen.

## Core model

Each feature has an MCM home, a logger and an output file.
A tool reads the game and its target mods, plays or forces content on demand, and appends lines to a file in the logs folder.
Saved game state and the target mods stay as the tool found them.

## Principles

- Dev-only. A tool or trace serves the user at the desk, and it may reach into a target mod's internals.
- Guard every reach. A call into a target mod checks that the mod is present first. An absent mod leaves that part of the tool dead.
- Each feature owns an MCM prefix and tab, a logger in `TZ_LOGGERS`, and an output file.
- Common code goes to xlibs. File system work is `xfs`, window building is `xcui`, and timers and slicing are `xslice`.

## Boundaries

- `_tz_manifest.script` and `_tz_init.script` hold the identity and the xlibs dependency gate.
- `tz_mcm.script` owns every option, and `build_path()` routes a key to its tab by prefix.
- `tz_help.script` opens a tool's manual page from the help elements in that tool's layout.
- `tz_ui.script` holds what every tool window shares beyond the xlibs primitives: the lamp pair, the disk browser's rows and the file of each game run.
- The tools are `tz_anim_tool.script`, `tz_geo_tool.script` and `tz_sound_tool.script`, each with its base layout and variants in `configs/ui`.
- `tz_anim.script` is the animation bench AnimationTool plays through.
- The traces are `tz_watcher.script`, `tz_perception.script` and `tz_sound_trace.script`.
- The scans are `tz_scanner.script` and `tz_geometry.script`.
- `tz_profiler.script`, `tz_loadgen.script` and `tz_loadgen_victim.script` are the profiler wraps and their test fixture.
- `tz_commands.script` and `tz_fx.script` are console benches run through `run_string`.
- `tz_remote.script` is the outside channel: the folder spool and the module reload.

## Runtime

- `on_key_press -> <tool>._on_key_press()`: when the key matches the tool's MCM bind, `_try_open()` returns on `Check_UI()`, else it shows the window and calls `Register_UI()`.
- In a tool window the user picks an item, then plays, steps or tags it. Each tag, call or note appends one line to the tool's file, and the bottom screen shows the last two.
- Esc with no edit box focused closes the window. `hide()` stops what the tool started and calls `Unregister_UI()`.
- `actor_on_net_destroy` cleans up what a window or trace holds.
- The stroll is a state machine whose state lives in the save. `start_stroll()` stores the route under `m_data.tz_stroll`, and each stop runs warp, clear, hour, weather, hold.
  A level change rebuilds the Lua state between `save_state` and `load_state`. `actor_on_first_update` then resumes the stored stop.
  A warp is `ChangeLevel` with no fade, to the stop's smart or the level's lowest game graph vertex.
  The weather goes through `level_weathers`' manager, whose check each game hour then keeps the cycle. `weather.pause` freezes it 2 s after the set, once the blend has run.
  The stop hours sit mid-band in the presets TestZone sets (`environment/weathers/w_clear1`, `w_rain1`, `w_storm1`, `w_foggy1`): clear 6, 12, 19 and 1, rain 12 and 0, storm 12 and 1, fog 13 and 1.
- The remote is a spool, Producer-Consumer over a folder. A 1 s timer takes the first `.lua` in `appdata/testzone/inbox` and removes it.
  It then runs the text under `xpcall`, requests a log flush, and writes a started record, then the result, to `outbox`.
  Each record carries the game's launch and its Lua state.
  A finished record ends on `end` and the run's name, and a reader waits for that line before it trusts the record.
  A file in `watch` runs when its text differs from its last run in this Lua state: on each load and edit. The writer renames a finished `.part` file to `.lua`, and a half-written file never runs.
  `error` is the dead-letter folder, and nothing clears it. A run that fails to compile or throws leaves its source and its result there, and a file the poll cannot read is copied there whole.
  When the poll starts, it closes each started record of an earlier Lua state. Within this launch the record becomes `reloaded`.
  From an earlier launch it becomes `stopped` and moves to `error` with its watch file. A watch file that crashed the game therefore never runs again.
  `testzone_remote_status.txt` is the heartbeat, written 1 s after the poll starts and every 5 s after that. A fresh file means the poll runs.
  It carries the launch, the Lua state, the level and the game hour.
- `tz_remote.load_module()` swaps a module for its edited source. It compiles first, then removes what the old module owns from five registries.
  They are the callbacks, the timers, the per-frame calls, the xsound listeners and the xbus subscribers.
  A function belongs to a module when its environment is the module table, or when a function it closes over has it. That covers TestZone's timer watch wrappers.
  It runs the new source under xray's module header, starts it, and lists the modules that still hold the old copy in a local.
- `tz_remote.load_all_scripts()` reloads every script of every mod: it rescans the scripts folder, saves as `tz_reload`, and loads that save once its `.scop` and `.scoc` exist. It gives up after 30 s.
  A load rebuilds the Lua state from the files on disk, with the game state taken from the save. It is complete where `load_module()` is fast.

## Rules and invariants

- A tool leaves saved game state untouched.
- A tool stops only the plays it started. SoundTool stops its handles and leaves every other sound playing.
- `on_key_press` fires before the UI takes the key. `Check_UI()` guards every open path, and a key typed into a note box opens no second window.
- A tool logs to `testzone.log` under its logger. A trace writes a separate file.
- An MCM key carries its feature's prefix: `geo_`, `soundtool_`, `perception_`, `profiler_`, `remote_`. A key with no prefix sits on the main tab.
- The remote runs code from a folder. It starts only with `-dbg`, a level loaded and the Remote switch on, and it never moves into xlibs.
- A reload refuses a module that declares a class, one an unlocalizer rewrites, and any module while `lua_use_functor_cache` is on, since each would keep old code running.
- The stroll puts back every setting it changed: the key prompt, god mode, the emission and psi storm options and the weather pause.

## UI rules

Layout:

- A tool's base layout places every box. Lua places no box from numbers in the script.
- A tool builds its controls with `xcui.build()` from one ordered list of layout names and kinds, which is also the draw order.
- After editing a base layout, rerun `stalker-manager.sh ui-variants TestZone` and commit the variants with it. A variant is never edited by hand.
- A list box is sized only in XML. Its scrollbar is placed once, when the list is built.
- A tool reopens where it closed: its view, selected row and scroll, kept in the script until the Lua state is rebuilt, which a save load and a level change both do. SoundTool rebuilds an open pool from its recipe, which copies its outside files again.
- A tool reads playlists: a `.txt` of one item per line or a `.tsv` whose first column is the item, a `;` line a comment.
  `[browse files]` at the root opens the logs folder, and `..` climbs to the drive list, through `tz_ui.get_browse_rows()`.
  AnimationTool lists `.tsv` and `.txt` files, SoundTool `.txt` and its folders of `.ogg`.
- A tool writes one file per game run in the logs folder through `tz_ui.write_line()`: `<tool>_<launch date>_<launch time>.txt`, every line tab-separated as time, source (the playlist, the pool or the level), what was pressed, the item, the tool's own details, the note last.
  The launch time is `os.time() - os.clock()`, since `os.clock` counts from process start, so every Lua state of one run finds the same file.
- AnimationTool docks right and GeoTool left, and the world stays visible beside them. SoundTool fills the middle.

Style, an analog Soviet device built from vanilla parts:

- Each tool is a separate device with a key family and a screen color of its kind, and every tool shares one title row.
  The title sits left, and the green lamp, the red lamp and the info key sit at the right edge.
- Every tool's keys are the PDA radio player's: text keys in the radio key's outline with a cut corner at each group end. SoundTool alone adds icon keys at their native 60x30.
- The info key is the PDA's circled i. The add-note key is a radio outline key with its plus drawn as two bars, so its stroke is heavier than a font's.
- A key wider than its texture draws in frame-line mode, its ends at native size and its middle repeated.
  `configs/ui/textures_descr/tz_ui.xml` names those parts as cut-outs of vanilla textures, and X-Ray stretches a plain button's texture to the button (`UI3tButton.cpp`, `CUI3tButton::DrawTexture`).
- A screen is a gray rim, (50,50,50), with its glass 1.5 units inside, both the vanilla white rectangle tinted in the layout.
  SoundTool's glass is dark with car-stereo orange text, AnimationTool's dark with soft cyan, and GeoTool's olive with dark text, the live readings on a lighter olive.
- Every window sits on the same opaque bevelled body, tinted rectangles named in `tz_ui.PANEL`. The note and the selected line sit on the screens' dark glass.
- A button acts on a click. A toggle holds a state and shows it lit. A texture is never stretched: frames and screens repeat their middle.
- Output shows on screens, lamps and lit keys. Input is the game's normal edit box.
- A green lamp lights while the tool runs something, and a red lamp lights when it stops. The unlit lamp is dimmed.
- The window carries the tool's name and the control labels, and no other text.
- The info key shows the tool's manual.

## Components

Each MCM tab is one component, in tab order.

- Main holds the callback watcher, the periodic stats, the deep introspection, the scans, the timer queue watch, the rate limit and the AnimationTool key.
  The watcher lists the 227 callbacks of vanilla, the overlay and our local build, and registers only those in axr_main's intercepts (`tz_remote.get_intercepts`).
  Its handlers only count and log. The per-frame and per-shot ones in `HIGH_RATE` wait for High-rate callbacks.
- Profiler wraps suspect functions with `xprofiler` and reports slow calls.
- Perception traces what the stalkers and mutants near the actor hear, see, remember and fear, and whom they pick as enemy.
- GeoTool holds its key and the refresh period. The window shows the position and every place check, refreshed while it is open.
  A note saves the spot with every reading to the GeoTool file of the run.
- SoundTool holds its key. The window plays channels, dread categories, voice and any `.ogg` on disk, and writes notes to the SoundTool file of the run.
  It names each file's producer, the X-Ray or vanilla code that plays it: a channel, the dread director, a script theme, a thunder strike, an NPC voice or a creature.
  `game` replays a game file the way its producer does, each click its own roll: a channel through xsound's copy of vanilla's channel play, dread through `dd_director.play_sound()`, an actor theme 2D at 0.8.
  An outside file gets no `game`, and shows where it would play. 2D plays at the ear, and 3D camera-forward at a preset or typed distance, with a height, and a falloff the two boxes override when both are filled.
  The MCM option `soundtool_mute` vetoes every play the seven engine sound seams reach, and the tool's own plays pass under a flag. It holds xray's level music factor at 0, which is never saved, and stops the actor's looped wind and rain.
  `on_game_start` applies it before any bed, wind or music starts, so a save and load with it on silences what was already playing, and an option change applies it at once. A level change or a load lifts it at `actor_on_net_destroy`, so the main menu keeps its music, and the next load applies it again.
  The channel index is xsound's one walk of the channel root. The theme and thunder indexes walk `script_sound.ltx` and `thunderbolts.ltx` once per open, and the open line logs their cost.
  A file from outside the game is copied into `sounds/sound_tool_cache`, and the index rescans once per batch. The game plays only indexed names.
- Remote holds the switch for the folder spool, written to `testzone_remote.log`. `stalker-manager.sh testzone-send` is its outside half.
- Development sets the log level and flush for every logger in `TZ_LOGGERS` and resets every option.
