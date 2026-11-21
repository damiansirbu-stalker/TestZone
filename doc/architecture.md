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
- `tz_ui.script` holds what every tool window shares beyond the xlibs primitives, the lamp pair.
- The tools are `tz_anim_tool.script`, `tz_geo_tool.script` and `tz_sound_tool.script`, each with its base layout and variants in `configs/ui`.
- `tz_anim.script` is the animation bench AnimationTool plays through.
- The traces are `tz_watcher.script`, `tz_perception.script` and `tz_sound_trace.script`.
- The scans are `tz_scanner.script` and `tz_geometry.script`.
- `tz_profiler.script`, `tz_loadgen.script` and `tz_loadgen_victim.script` are the profiler wraps and their test fixture.
- `tz_commands.script` and `tz_fx.script` are console benches run through `run_string`.

## Runtime

- `on_key_press -> <tool>._on_key_press()`: when the key matches the tool's MCM bind, `_try_open()` returns on `Check_UI()`, else it shows the window and calls `Register_UI()`.
- In a tool window the user picks an item, then plays, steps or tags it. Each tag, call or note appends one line to the tool's file, and the bottom screen shows the last two.
- Esc with no edit box focused closes the window. `hide()` stops what the tool started and calls `Unregister_UI()`.
- `actor_on_net_destroy` cleans up what a window or trace holds.

## Rules and invariants

- A tool leaves saved game state untouched.
- A tool stops only the plays it started. SoundTool stops its handles and leaves every other sound playing.
- `on_key_press` fires before the UI takes the key. `Check_UI()` guards every open path, and a key typed into a note box opens no second window.
- A tool logs to `testzone.log` under its logger. A trace writes a separate file.
- An MCM key carries its feature's prefix: `geo_`, `soundtool_`, `perception_`, `profiler_`. A key with no prefix sits on the main tab.

## UI rules

Layout:

- A tool's base layout places every box. Lua places no box from numbers in the script.
- A tool builds its controls with `xcui.build()` from one ordered list of layout names and kinds, which is also the draw order.
- After editing a base layout, rerun `stalker-manager.sh ui-variants TestZone` and commit the variants with it. A variant is never edited by hand.
- A list box is sized only in XML. Its scrollbar is placed once, when the list is built.
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
- Profiler wraps suspect functions with `xprofiler` and reports slow calls.
- Perception traces what the stalkers and mutants near the actor hear, see, remember and fear, and whom they pick as enemy.
- GeoTool holds its key and the refresh period. The window shows the position and every place check, refreshed while it is open.
  A note saves the spot with every reading to `geo_tool.txt`.
- SoundTool holds its key. The window plays ambience channels, dread categories, voice and any `.ogg` on disk, and writes tags and notes to `sound_tool.txt`.
  A file from outside the game is copied into `sounds/sound_tool_cache`, and the index rescans once per batch. The game plays only indexed names.
- Development sets the log level and flush for every logger in `TZ_LOGGERS` and resets every option.
