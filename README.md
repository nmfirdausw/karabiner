# Karabiner Elements Config

Personal [Karabiner-Elements](https://karabiner-elements.pqrs.org/) configuration for macOS. The active profile (`CANARY`) replaces the physical QWERTY layout with a custom Canary-style letter layout, adds home row mods, a navigation layer, a symbol layer, and a handful of chorded shortcuts.

## Profiles

| Profile | Purpose |
|---|---|
| `QWERTY` | Stock fallback. No remaps — useful for troubleshooting or handing the keyboard to someone else. |
| `CANARY` | The daily-driver profile. Selected by default. Everything below describes this profile. |

Switch profiles from the Karabiner-Elements menu bar icon, or **Preferences → Profiles**.

## How the layers stack

Karabiner evaluates complex modification rules top to bottom, and a physical key event is consumed by the first manipulator that matches it. The rules in this config are ordered deliberately:

1. **Chord Combo** — simultaneous physical key presses (see below)
2. **Space Layer** — hold Space for arrows/numbers
3. **Command Layer** — hold Command for symbols
4. **Modifier bypass for home row mods** — makes held-modifier + `j`/`k`/`l` repeat correctly
5. **Home Row Mods** — `a s d f` / `j k l ;` as dual-role mod-tap keys
6. **Canary Layout** — the base letter remap

Because the layers above the base layout run first, the Space layer, Command layer, chord combos, and the modifier-bypass rule all key off **physical** QWERTY positions, not the Canary output letters. Home row mods sit just above the base layout, so their "tap" output already matches what Canary would produce for that key (e.g. tapping physical `A` always sends `c`, whether or not it briefly looked like a modifier hold).

## Canary base layout

Physical key (ANSI QWERTY position) → character sent.

| Row | Physical | Output |
|---|---|---|
| Top | `Q W E R T` | `w l y p b` |
| Top | `Y U I O P` | `z f o u '` |
| Home | `A S D F G` | `c r s t g` |
| Home | `H J K L ;` | `m n e i a` |
| Bottom | `⇧(L) Z X C V` | `q j v d k` |
| Bottom | `B N M , . /` | `b x h / , .` |

Notes:
- `G` and `B` are not remapped — they pass through unchanged.
- The physical **left Shift** key sends `q` (an angle-mod style extra column), so left Shift itself is not available as a plain modifier tap in this profile — modifier duty on the left hand comes from the home row mods below instead.
- `virtual_hid_keyboard.keyboard_type_v2` is set to `ansi`, so this assumes a physical ANSI keyboard.

## Home row mods

`a s d f` and `j k l ;` are dual-role: tap for a letter, hold for a modifier. Both hands mirror each other (pinky→Control, ring→Option, middle→Shift, index→Command).

| Physical key | Tap | Hold |
|---|---|---|
| `A` | `c` | Left Control |
| `S` | `r` | Left Option |
| `D` | `s` | Left Shift |
| `F` | `t` | Left Command |
| `J` | `n` | Right Command |
| `K` | `e` | Right Shift |
| `L` | `i` | Right Option |
| `;` | `a` | Right Control |

Timing: 150ms for the alone/held-down/delayed-action thresholds. Each key uses the standard hybrid home-row-mod setup (`to_if_alone`, `to_if_held_down`, and a `to_delayed_action` → `to_if_canceled` fallback) to cut down on misfires when rolling keys quickly while typing.

### Modifier bypass (repeat fix)

Home row mods normally break OS key-repeat once a modifier is "held" through Karabiner's mod-tap logic. The **Modifier bypass** rule works around this for the right hand by matching mandatory Control/Option/Command/Shift + physical `j`/`k`/`l` directly and re-emitting the same modifier with `n`/`e`/`i` — so holding a modifier and repeatedly tapping `J`/`K`/`L` reliably repeats (e.g. Cmd+J held-repeat → Cmd+N).

## Space layer

Hold **Space** to activate; tapping Space alone still sends a space.

| Physical | Output |
|---|---|
| `H J K L` | ← ↓ ↑ → (arrow keys) |
| `Q W E R T Y U I O P` | `1 2 3 4 5 6 7 8 9 0` |

## Command layer (symbols)

Holding either **Command** key activates a symbol layer over the letter/punctuation keys — useful for typing symbols without a pinky stretch. Command is still sent through as a normal modifier, so existing Cmd+key app shortcuts are shadowed by this layer.

| Physical | Symbol | Physical | Symbol |
|---|---|---|---|
| `Q` | `` ` `` | `H` | `:` |
| `W` | `(` | `J` | `-` |
| `E` | `)` | `K` | `[` |
| `R` | `^` | `L` | `]` |
| `T` | `&` | `;` | `;` |
| `Y` | `\|` | `X` | `*` |
| `U` | `$` | `C` | `%` |
| `I` | `{` | `N` | `\` |
| `O` | `}` | `M` | `_` |
| `P` | `"` | `,` | `?` |
| `A` | `!` | `.` | `<` |
| `S` | `@` | `/` | `>` |
| `D` | `+` | Left Shift | `~` |
| `F` | `=` | | |
| `G` | `#` | | |

## Chord combos

Pressing two physical keys at (almost) the same time sends a single key, regardless of the active layer:

| Chord | Sends |
|---|---|
| `K` + `L` | Enter |
| `I` + `O` | Backspace |
| `S` + `D` | Tab |
| `W` + `E` | Escape |

## Installation

1. Install [Karabiner-Elements](https://karabiner-elements.pqrs.org/).
2. Copy (or symlink) `karabiner.json` into `~/.config/karabiner/`.
3. Open Karabiner-Elements → **Preferences → Profiles** and select `CANARY`.

## Repo structure

```
karabiner.json                    Karabiner-Elements config (profiles, rules)
assets/complex_modifications/     Empty — reserved for imported rule JSON files
automatic_backups/                Karabiner's own auto-backups (git-ignored)
```

## Caveats

- No per-device overrides are configured — the CANARY profile applies to every keyboard.
- No `simple_modifications` or `fn_function_keys` overrides are set; only `complex_modifications` are in use.
- This is tuned to one person's typing feel (150ms mod-tap thresholds, ANSI layout). Adjust the `basic.to_if_alone_timeout_milliseconds` / `basic.to_if_held_down_threshold_milliseconds` parameters in the Home Row Mods rule if taps are misfiring as holds (or vice versa).
