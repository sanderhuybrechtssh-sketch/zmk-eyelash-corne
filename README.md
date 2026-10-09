# Eyelash Corne — my ZMK config

ZMK firmware for my [Eyelash Corne](docs/UPSTREAM_README_EN.md) (nice!nano v2, 42 keys + 5-way joystick + rotary encoder).

The layout is a port of my [kanata](https://github.com/jtroo/kanata) config: **Colemak-DH with bilateral home row mods**, a **nav layer** on the caps-lock key and a **mouse layer** on the left thumb. Windows stays set to the **English (UK)** layout; every symbol in this keymap is chosen so it comes out right on that layout.

![Keymap](keymap-drawer/eyelash_corne.svg)

*Legend: the big label is what a tap sends, the small label at the bottom is the hold action, the small label on top is the shifted character. Highlighted keys are the ones held to reach that layer.*

## Layers

| # | Layer | How to reach it | What's on it |
|---|-------|-----------------|--------------|
| 0 | **Colemak** | default | letters, home row mods |
| 1 | **Number** | hold outer left thumb | digits, Bluetooth profiles, RGB, arrows, Home/End/PgUp/PgDn |
| 2 | **Symbol** | hold middle right thumb | all symbols (UK-correct), USB/BLE output switch |
| 3 | **Fn** | hold Space or Enter (inner thumbs) | F1–F12, bootloader, reset, ZMK Studio unlock |
| 4 | **Nav** | hold the key left of `A` (caps-lock position) | arrows, numbers, plain modifiers, PgUp/PgDn / desktop switch |
| 5 | **Mouse** | hold middle left thumb | pointer, scroll, clicks, Neru shortcuts |

### Colemak (base)

```
Tab   Q    W    F    P    B          ↑          :;   L    U    Y    M    Bksp
Nav   A    R    S    T    G     ←  Enter  →     J    N    E    I    O    '
\     Z    X    C    D    V    Spc     ↓        K    H    ,    .    /    Esc
                Num  Mouse Spc/Fn                Ent/Fn Sym AltGr
```

- My own Colemak-DH variant: `; L U Y M` on the top right and `J N E I O` on the home row (same as in kanata).
- The bottom row is *not* angle-shifted like on my laptop, because the Corne has straight columns.
- `:;` key: types `:`, with Shift types `;`. With Alt held it sends `Alt+;` (for Neru) instead of `Alt+Shift+;`.
- Middle cluster: the joystick is the arrow keys + Enter, the encoder button is Space, turning the encoder changes volume.

### Home row mods

| Left hand | | Right hand | |
|---|---|---|---|
| `A` | Gui (Win) | `O` | Gui (Win) |
| `R` | Alt | `I` | Alt |
| `S` | Ctrl | `E` | Ctrl |
| `T` | Shift | `N` | Shift |

They're **bilateral**: a home row key only becomes a modifier when you press a key on the **other hand** (or a thumb/joystick key) while holding it. Rolling over keys on the same hand always types letters, so fast typing doesn't trigger modifiers by accident.

Timings (in [`config/eyelash_corne.keymap`](config/eyelash_corne.keymap), behaviors `hml`, `hmr`, `hmls`, `hmrs`):

| Setting | Value | Meaning |
|---|---|---|
| `tapping-term-ms` | 240 (Shift: 200) | how long to hold before it counts as a modifier |
| `quick-tap-ms` | 160 | tap then hold quickly = repeat the letter instead |
| `require-prior-idle-ms` | 150 | while typing fast, home row keys are always letters |

### Nav (hold the key left of `A`)

```
 .    1    2    3    4    5                     6    →   PgUp  9    0    .
HELD Gui  Alt  Ctrl Shft  .                     ↓   Shft Ctrl  Alt  Gui   .
 .    .    .    .    .    .                     ↑    ←   PgDn  .    .    .
```

- Same key positions as my kanata nav layer.
- **Virtual desktops:** hold Gui (left hand, on this layer) and press PgUp/PgDn → sends `Win+Ctrl+←/→` to switch Windows virtual desktops.

### Mouse (hold the middle left thumb)

```
 .    .    .   F13   .   F17                    .    .   M↑    .    .    .
 .    .    .   F16   .   F14                  Scr↓  M←   M↓   M→    .    .
 .    .    .   F15   .    .                   Scr↑   .    .    .    .    .
                .  HELD LClick              RClick   .    .
```

- `U N E I` move the pointer, `J`/`K` scroll, Space = left click, Enter = right click.
- F13–F17 are bound in Neru: F13 hints, F14 grid, F15 recursive grid, F16 scroll, F17 bisect.

### Number / Symbol / Fn

These come from the stock Eyelash keymap. The Symbol layer was changed so the symbols are correct on the **UK** Windows layout (`@` = Shift+`'`, `#`/`~` = ISO hash key, `\`/`|` = ISO key left of Z, `"` = Shift+2).

Useful keys:

| Keys | Action |
|---|---|
| Num + `R` / `S` / `T` / `G` | Bluetooth profile 0 / 1 / 2 / 3 |
| Num + `A` | clear **all** Bluetooth pairings |
| Sym + `Z` / `X` | output to USB / Bluetooth |
| Fn (hold Space) + `C` | **bootloader on the left half** (flashing mode) |
| Fn (hold Space) + `,` | **bootloader on the right half** |
| `Q` + `R` + `Z` held 2 s | soft off (deep sleep), wake with the reset button |

## Changing the layout

1. Edit [`config/eyelash_corne.keymap`](config/eyelash_corne.keymap) and push.
2. Builds don't start automatically on this fork: go to **Actions → Build ZMK firmware → Run workflow**.
3. Download the `firmware` artifact from the finished run. It contains:
   - `eyelash_corne_studio_left.uf2` → left half (the "central" half; the keymap lives here)
   - `eyelash_corne_right nice_view-nice_nano_v2-zmk.uf2` → right half
   - `settings_reset-nice_nano_v2-zmk.uf2` → wipes stored settings and Bluetooth pairings
4. Flash: plug a half in over USB, enter the bootloader (Fn + `C` for the left half, Fn + `,` for the right half, or double-tap the reset button), and copy the `.uf2` onto the `NICENANO` drive.

Keymap-only changes only need the **left** half reflashed.

To regenerate the picture above:

```bash
pip install keymap-drawer
keymap -c keymap_drawer.config.yaml parse -z config/eyelash_corne.keymap > keymap-drawer/eyelash_corne.yaml
keymap -c keymap_drawer.config.yaml draw -j config/eyelash_corne.json keymap-drawer/eyelash_corne.yaml > keymap-drawer/eyelash_corne.svg
```

(or run **Actions → Draw Keymap**). On Windows set `PYTHONUTF8=1` first.

## Gotchas

- **ZMK Studio** only works with the **left half plugged in over USB**, not over Bluetooth. Changes saved in Studio **override** the keymap file even after reflashing; use *Restore Stock Settings* in Studio (or flash `settings_reset`) to go back to the file.
- **kanata**: the `winIOv2` build remaps *every* keyboard, including this one, which double-remaps the letters. Quit kanata while using the Corne.

## Credits

Board files and stock keymap: [a741725193/zmk-new_corne](https://github.com/a741725193/zmk-new_corne) — original docs in [docs/](docs/UPSTREAM_README_EN.md). Diagram by [keymap-drawer](https://github.com/caksoylar/keymap-drawer).
