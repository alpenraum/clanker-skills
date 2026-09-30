# Design — visual identity across apps

One visual language for every app. **Colours are chosen per app; everything else here is fixed.**
Derived from the Scrubradius source (`~/Documents/scrubradius`, `composeApp/.../ui/base/compose/theme/`
and `ui/components/`), with its contrast gaps corrected. The design-system rules in
[general.md](general.md#design-system) still apply: no hardcoded visual values, ask before adding a token.

## Character

- An instrument, not a brochure: monospace headings and numbers, flat outlined surfaces, one saturated
  primary, accents only where they carry meaning.
- No shadows, no gradients, no decorative imagery. Separation comes from 1 dp borders and tone steps.
- Labels shout (uppercase), content talks (sentence case).

## Theme mode

- **One mode per app: dark or light.** Decided per app, recorded in that repo's `docs/conventions.md`.
  The app ships that mode only — no theme toggle is required.
- Scrubradius is a dark app.

## Colour

### Roles

Neutrals:

| Token | Use |
|---|---|
| `page` | Screen background |
| `surface` | Cards, list rows, inputs. Same tone as `page` — a card is defined by its border, not its fill |
| `surface-raised` | Bottom sheets |
| `surface-overlay` | Dialogs |
| `surface-track` | Segmented-toggle track, progress track |
| `border` | 1 dp outline of every card, row, input, outline button |
| `text` | Primary text and icons |
| `text-muted` | Section labels, subtitles, captions, placeholders, units |
| `scrim` | Behind modals: black |

Four hues per app — `primary` plus three semantic accents:

| Hue | Means | Examples |
|---|---|---|
| `primary` | Default action, active, selected, focus, info, **success** | Save, Next, stable reading, PASSED |
| `warn` | Needs attention, not a failure | Low-confidence reading, retry reference |
| `cancel` | Backing out without harm | Cancel, Skip |
| `error` | Failure or destruction | FAILED, unreliable result, Discard, swipe-to-delete |

There is no separate success colour: success is `primary`. Warn, cancel and error must stay distinct
hue families — keep them yellow-ish, orange-ish and red-ish respectively; the exact hues are per app.
Every accent keeps **≥ 25° hue distance** (LCh) from `primary` and from each other. When the primary sits
in one of those families (an orange key colour, say), `cancel` goes achromatic — a neutral grey on the
same tone rules — and `error` moves to a true red.

Each hue expands to four tokens:

| Token | Use |
|---|---|
| `X` | Fill of filled buttons, selected segment, swipe background |
| `on-X` | Text and icons on `X` |
| `X-text` | The hue used as text, icon, outline or focus border on a neutral surface |
| `X-container` | Tinted background of status blocks and "locked/stable" cards. Its content is `X-text` (dark) or `X-on-container` (light) |

Disabled is not a colour: fill at 12 % alpha, content at 38 % alpha of the enabled colours.

### Tone rules

Tone = CIELAB L\* (0 black … 100 white; Material's HCT tone is the same scale).

| Token | Dark app | Light app |
|---|---|---|
| `page`, `surface` | 5–7, achromatic | 98–100 |
| `surface-raised` / `-overlay` / `-track` | 10 / 17 / 22 | 96 / 92 / 90 |
| `border` | 45–50 | ≤ 50 on interactive boundaries; 80 allowed for purely decorative card outlines |
| `text` | 98 | 10 |
| `text-muted` | ≥ 60 | ≤ 30 |
| `primary` fill | ~40, with `on-primary` = near-white — or, for a light key colour (65–90), the key colour itself with near-black | same |
| accent fills | 65–90, with `on-X` = near-black | same |
| `X-text` | primary 80; accents = their fill (so ≥ 65) | 40 |
| `X-container` | 10–20, chroma ≤ 35 | 92, chroma ≤ 25 |
| `X-on-container` | — (use `X-text`) | 30 |

Fills and their inks are mode-independent. Near-black = L\* ~3 (`#0A0A0A`), near-white = L\* ~98 (`#FAFAFA`).

### Contrast — WCAG AA, always

- Text ≥ 4.5 : 1; large text (≥ 24 sp, or ≥ 18.7 sp bold) ≥ 3 : 1; icons and interactive boundaries ≥ 3 : 1.
  Disabled states are exempt.
- Check against the lightest surface a token can land on in dark mode (`surface-overlay`) and the darkest
  in light mode (`surface-track`), not just `page`.
- A new palette ships with its contrast table in the app's `docs/conventions.md`. Pairs to check:
  `text`/`text-muted` on every surface · `border` on `surface`/`surface-raised` · `on-X` on `X` ·
  `X-text` on every surface · container content on `X-container`.

### Deriving an app palette

1. Choose the mode.
2. Pick four seed hues: primary, and yellow-, orange-, red-family accents.
3. Generate each token at the tone in the table, keeping the seed's hue; reduce chroma until it fits sRGB.
4. Build the contrast table; any pair below AA moves its tone outward, never the rule.

### Worked example — Scrubradius (dark)

All pairs verified; worst case in brackets is on `surface-overlay`.

| Token | Value | Contrast |
|---|---|---|
| `page` / `surface` | `#131313` / `#141414` | — |
| `surface-raised` / `-overlay` / `-track` | `#1C1B1B` / `#2B2A2A` / `#353434` | — |
| `border` | `#737373` | 3.9 on surface (3.0) |
| `text` | `#FAFAFA` | 17.6 |
| `text-muted` | `#969696` | 6.2 (4.8) |
| `primary` / `on-primary` | `#5A3AE1` / `#FAFAFA` | 6.4 |
| `primary-text` | `#C8BFFF` | 10.8 (8.4) |
| `primary-container` | `#1D1B2C` | 9.9 with `primary-text` |
| `warn` / `warn-container` | `#F4DC4D` / `#383000` | 14.3 with `#0A0A0A`; 9.6 as text on container |
| `cancel` / `cancel-container` | `#F5A359` / `#4C2700` | 9.7; 6.4 |
| `error` / `error-container` | `#F18777` / `#5E170F` | 8.0; 5.3 |

The same hues in a light app: text tones `#5A3AE1` · `#6C5E00` · `#8E4E06` · `#9A4337` (all ≥ 5.0 on
`#E5E2E1`), containers `#EEE4FF` · `#F6E8B8` · `#FFE3CF` · `#FFE2DD` with content `#3A23C7` · `#504702` ·
`#6D3A00` · `#822620` (≥ 7.6), text `#1C1B1B`, muted `#444748`, border `#747878`.

## Typography

- **Two families.** Monospace — JetBrains Mono (variable, bundled) — for every `display*`, `headline*` and
  `title*` role. The platform sans (Roboto / SF, i.e. the default family) for every `body*` and `label*` role.
- **Sizes are the Material 3 baseline**; only the family is overridden.

| Role | sp / line height | Used for |
|---|---|---|
| `displayMedium` | 45 / 52 | Screen title |
| `headlineLarge` | 32 / 40 | Live reading, primary value on a card |
| `headlineMedium` | 28 / 36 | Metric value in a result grid |
| `headlineSmall` | 24 / 32 | Step heading, dialog title, derived value |
| `titleMedium` | 16 / 24 | Compact step heading |
| `bodyMedium` | 14 / 20 | Body text, list-row title, input text, toggle labels |
| `bodySmall` | 12 / 16 | Section label, card title, subtitle, unit, empty-state line |
| `labelLarge` / `labelMedium` / `labelSmall` | 14 / 12 / 11 | Dialog buttons, status line under a reading, PASS/FAIL tag |

- **Section labels and card titles**: `bodySmall`, uppercase, `text-muted`, single line, ellipsised.
- **Buttons**: label uppercase, weight 600. Text buttons: bold, uppercase, underlined.
- **Numbers**: unit symbol attached (`-2.5°`, `7mm`); fixed decimals per quantity, never variable; value
  and unit may split into `headline*` value + `bodySmall` unit. Separators in data lines are ` · `.
- Values that must fit one line shrink in 10 % steps instead of wrapping.

## Shape, borders, depth

- **Corner radius 8** on every rectangular component: card, list row, button, input, status block,
  swipe background. **Full pill** for segmented toggles, pager dots and chips. Dialogs and bottom
  sheets keep the platform container shape (Material: 28 dp).
- **Border 1 dp**, `border` colour, on every card, row, input and outline button. Emphasis changes the
  border colour, never its width.
- **Elevation 0.** Buttons, cards and rows carry no shadow. Depth is a tone step (`surface-raised`,
  `surface-overlay`), not a shadow.
- **Minimum size 48 × 48** for anything tappable, including cards and rows.
- **State through colour**: a focused input or a live-reading card takes a `primary-text` border; a
  stable or locked reading animates its fill to `primary-container`.
- **Neon glow** is the one expressive effect, reserved for rare emphasis or an easter egg: three
  rounded rects outside the element at spreads 12 / 6.6 / 3.4 dp and alpha 8 / 15 / 25 %, a 1.5 dp
  stroke at 80 %, all in `primary`; fades in over 400 ms, pulses 85–100 % over 1200 ms.

## Spacing and layout

- **4-point grid: 4 · 8 · 12 · 16 · 24.** 8 is the default gap between siblings.
- Screen padding 16 (Scrubradius's measurement screens use 8 — follow 16). Card padding 16, card title → content 8. List row padding 8, leading icon → text 4.
  Chip padding 12 × 8. Toggle segment padding 12. Dialog padding 24, title/icon → text 16, text → actions 24.
- Forms and actions are **full width** with 16 horizontal padding inside sheets; fields 8 apart; 16
  before the primary action.
- **Screen skeleton**: title → content in one scrolling column → actions at the end.
- **Measurement flow**: instructions (rows with a leading info icon) → primary *Start* → choice (outline
  buttons) → live card + step indicator → primary *Next*, enabled only when the reading is stable →
  results.
- **Results**: values in cards laid out two per row, 8 apart, equal weight. Action row: outline *Retry*
  and filled *Save* side by side, equal weight; destructive *Discard* as an `error` text button below.
- **Status next to data**: an `Info` block in `warn` or `error` directly under the value it qualifies.

## Components

Every component takes a **level** — `default`, `warn`, `cancel`, `error` — mapping to `primary`, `warn`,
`cancel`, `error`. A component never picks a hue itself.

| Component | Contract |
|---|---|
| Filled button | Fill `X`, content `on-X`, radius 8, no elevation, optional leading icon 4 before the label |
| Outline button | Transparent, 1 dp border in `X-text`, label in `primary-text` |
| Text button | No container; bold uppercase underlined label in `X-text` |
| Card | `surface` + `border`, radius 8, padding 16. Optional header row: uppercase title left, secondary content right. Optional tap target |
| List row | Card styling, padding 8; title `bodyMedium`, subtitle `bodySmall` muted; leading and trailing icons take `X-text` |
| Text input | Transparent fill, single line, min height 48; border `border` → `primary-text` on focus → `error-text` on error → 12 % alpha when disabled; placeholder `text-muted`; icons follow the border colour |
| Info / status block | `X-container` fill, 1 dp `X-text` border, content `X-text` (dark) / `X-on-container` (light), `bodyLarge`, padding 8, min 48 × 48 |
| Segmented toggle | Pill track `surface-track`; a `primary` pill slides to the selection; labels animate `text` ↔ `on-primary`; segments equal width |
| Step indicator | Pill dots 8 dp, 8 apart, `text`; the active one stretches to 16 dp and turns `primary-text` |
| Bottom sheet | Modal, never half-expanded; compact (wraps content) or full-height (screen − 64 dp); uppercase label heading |
| Dialog | Padding 24; optional icon (centres the layout); `headlineSmall` title; body `bodyMedium`; text buttons bottom-right, 8 apart |
| Swipe to delete | End-to-start only; `error` fill with `on-error` delete icon behind the row; always confirmed with a dialog |
| Loading | Indeterminate circular indicator. Timed work: linear progress, animated with linear easing |
| Diagram | The measured quantity in `primary-text` 2 dp; object outlines in `text-muted` 2 dp; reference and centre lines 1 dp |

## Iconography

- **Material Symbols, Outlined.** The Filled variant only for the selected state (navigation, toggles).
  No other set in the same app.
- Icons inherit the component's level colour; they never carry their own.
- App icon: line art in two strokes — the primary hue and `text` — on a dark neutral tile.

## Motion

- Between different screen states (loading → loaded → result): crossfade, keyed by state *type* so
  updates within one state don't flash.
- Appear / disappear: fade, 400 ms.
- Colour, size and position changes animate (selection pill slides, dot stretches, card fill tints).
  No bounce or overshoot.
- Timed progress: linear easing over its real duration.

## Content and voice

- Uppercase: buttons, section labels, card titles, status tags (PASS / FAIL). Everything else sentence case.
- Instructions are short imperatives, one per row: "Hold your phone flat with the screen facing up".
- Errors say what is wrong and by how much: "Unreliable: error 0.8° exceeds 0.5°".
- Humour is allowed for absurd input, never on a real error ("Great Scott! Is that a flux capacitor
  under the hood?" for a car from the future).

## Platform mapping

- **Compose**: `MaterialTheme` carries typography and the Material colour roles; an `@Immutable`
  extended-colour class in a `staticCompositionLocalOf` carries the tokens Material lacks, read as
  `AppTheme.colors.X`. Radius, border, disabled alphas and minimum size live in one `UiDefaults` object;
  the level enum resolves `X` / `on-X` / `X-container`.
- **Flutter**: `ThemeData` + a `ThemeExtension` for the extended tokens; `TextTheme.copyWith` swaps
  the family per role.
- **Web**: CSS custom properties for every token (`--color-primary-text`, `--radius: 8px`, …);
  JetBrains Mono self-hosted via `@font-face`.

## Do not copy from Scrubradius

- `Button.kt` swaps the disabled alphas (38 % on the fill, 12 % on the label). The rule is 12 % fill, 38 % content.
- `primary` (`#5A3AE1`) used as text, border or `Info` content on a dark surface — 2.7 : 1. Use `primary-text`.
- `#737373` as muted text (3.9 : 1) and the `onXContainer` colours (~2 : 1).
- Four Material icon sets mixed; `Color.White` hardcoded in `CalibrationFeature.kt`; UI strings inline
  instead of resources.
