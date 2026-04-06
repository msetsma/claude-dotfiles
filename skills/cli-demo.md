## Metadata
name: cli-demo
description: Create or update a demo/ directory with a VHS tape file that generates a terminal demo GIF, then reference it in README.md. Use when the user asks to create a demo, update a demo, record a CLI demo, or add a GIF to the README.
context: fork
allowed-tools: Read Write Edit Glob Grep Bash


# Create Demo

Create or update a `demo/` directory with a [VHS](https://github.com/charmbracelet/vhs) tape file that records a terminal demo GIF, then reference that GIF in `README.md`.

## Prerequisites

Confirm `vhs`, `ffmpeg`, and `ttyd` are on `$PATH`. If missing, provide installation instructions and stop.

## Procedure

### 1. Check for `demo/` at the repo root

**If `demo/` does not exist:** create it, then proceed to step 2.
**If `demo/` exists and contains `demo.tape`:** skip to step 3.
**If `demo/` exists but has no `demo.tape`:** proceed to step 2.

### 2. Create `demo/demo.tape`

Analyze the project — read the entrypoint, CLI help output, README, and relevant source files to understand the primary commands, flags, and typical usage flow. Then write a `demo.tape` tailored to the project using this template as a base:

```tape
Output demo/demo.gif

Require <binary_name>

Set Shell "bash"
Set FontSize 14
Set Width 1200
Set Height 600
Set Theme "Catppuccin Mocha"
Set WindowBar Colorful
Set Padding 20
Set Margin 10
Set MarginFill "#1e1e2e"
Set BorderRadius 8
Set TypingSpeed 50ms
Set CursorBlink false

# Use Hide/Show for setup & cleanup (building binaries, seeding data, etc.)

Type "<command>"
Sleep 500ms
Enter
Sleep 3s
```

**Tape guidelines:**
- `Output` must be `demo/demo.gif`.
- Use `Require` for the project's binary.
- Use `Hide`/`Show` for setup/teardown so only the demo is visible.
- Keep demos concise: 5–15 seconds of recording.
- Use `Sleep` after output so viewers can read it.

Then run `vhs demo/demo.tape` and confirm `demo/demo.gif` was generated.

### 3. Update existing `demo/demo.tape`

Assume the demo needs updating — do not prompt for confirmation. Perform the following:

1. Read the existing tape and understand what it currently demonstrates.
2. Analyze the current project state — look for new/renamed/removed commands, flags, subcommands, or changed output formats since the tape was last written.
3. Update the tape to reflect the current project. Preserve the existing style, theme, and pacing — only change what's stale or missing. Add new notable features; remove references to anything that no longer exists.
4. Run `vhs demo/demo.tape` to regenerate the GIF.

### 4. Update `README.md`

Check if `README.md` already references `demo/demo.gif`. If not, add the following near the top of the file (after the title/badges, before the first section):

```markdown
## Demo

![Demo](demo/demo.gif)
```

If a reference already exists, ensure it points to `demo/demo.gif`. Do not create duplicates.

## Notes

- `demo.tape` is the source of truth; `demo.gif` is a generated artifact. Both should be committed.
- To regenerate: `vhs demo/demo.tape` from repo root.
- If the GIF exceeds ~10MB, reduce demo length, lower framerate (`Set Framerate 30`), or shrink terminal dimensions.