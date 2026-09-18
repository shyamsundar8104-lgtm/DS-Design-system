# Guidelines.md — Design System 1.0 Build Guidelines

Figma file: `Design System 1.0` (fileKey: `G476EIZkToaQPfb49fC4wY`)

This is the implementation guide that ties together the other three references:
- **`tokens.md`** — the exact values (what to bind)
- **`components.md`** — the real variant structure per category (what exists)
- **`style.md`** — the visual/voice rules (how it should feel)

Guidelines.md is the "how to actually build with all of that" layer — the rules an engineer or an AI builder (e.g. Figma Make) needs to follow to produce something that's actually *this* system, not a generic approximation of it.

---

## 1. Token architecture (from `tokens.md`)

5 variable collections. Always bind to the most specific layer available — never hardcode a hex/px value if a token exists for it.

| Collection | Tokens | Modes |
|---|---|---|
| Primitive Colors | 245 | Mode 1 |
| Semantic Colors | 170 | Light / Dark |
| Typography | 13 | Desktop / Tablet / Mobile |
| Spacing and Sizing | 82 | Desktop / Tablet / Mobile |
| Effects | 48 | Mode 1 |

**Alias chain:** `Component` → `Semantic` → `Primitive`. Never bind a component straight to a Primitive — that breaks Light/Dark theming, since Primitives have no mode.

**Naming convention:** first path segment capitalized, everything after that lowercase — `Bg/button/primary/default`, `Text/button/tertiary/default`, `Border/checkbox/hover`. Any new token must follow this.

**Hard rule for AI builders:** the full token list lives in `tokens.md`. Do not substitute a generic/default theme's variable names (e.g. a starter template's `--color-sidebar`, `--color-chart-1..5`) — those don't exist in this system and silently replace it with a different one.

---

## 2. Color usage (from `tokens.md` §2 + `style.md` §2)

- Bind Semantic tokens only. Pick by **purpose**, not by look — "the button's default background" is `Bg/button/primary/default`, not a violet that looks close enough from the Primitive ramp.
- Mode switching: identical token *names* between Light and Dark, only the resolved value changes (via `data-theme` attribute in code, mode toggle in Figma).
- **Disabled states** use reduced opacity of the default token (`Bg/button/disable-opacity` = 40%), not a separate flat color.
- **Destructive actions** use the dedicated `Bg/button/destructive/*` / `Border/button/*/destructive` branch, never a manually darkened red.
- **Status color mapping** — map any new status to one of these five, don't invent a sixth:

  | Meaning | Token family |
  |---|---|
  | Success | `.../success` (green) |
  | Warning | `.../warning` (orange) |
  | Error / destructive | `.../error`, `.../destructive` (red) |
  | Info / in-progress | `.../information` (blue) |
  | Neutral | `.../neutral` (gray) |

---

## 3. Spacing & grid (from `tokens.md` §4)

- Base unit: `Layout/base grid unit` = **8px**, consistent across Desktop/Tablet/Mobile.
- 12-step Spacing scale (`xxs` 4px through `7xl` 96px) — every padding/gap/margin must be one of these values. If a layout seems to need something else, that's a layout problem, not a reason for a new token.
- Component-specific sizing (Button, Button/toggle, Input field, Checkbox, Dropdown/select-list, Text line-height, Modal sizes) lives in the same collection — use those instead of recalculating dimensions by hand. Full table in `tokens.md` §4.
- **Correction from an earlier audit:** `Button/md` and `Button/sm` each have their own dedicated padding/radius tokens — they do *not* reuse `Button/lg`'s. The one place that pattern actually exists is **Toggle** (`Button/toggle/sm/radius` shared across sizes) — treat that as the one exception to double-check, not the norm.

---

## 4. Typography (from `tokens.md` §3)

- 13 tokens: 2 font families (Primary = SF Pro, Secondary = Consolas), 7 sizes (H1→tiny), 4 weights (regular/medium/semibold/bold) — each responsive across Desktop/Tablet/Mobile.
- Bind the token, never a raw font-size, so text reflows correctly per breakpoint automatically.
- Don't mix more than 2 weights in one view; Bold is reserved for page-level titles (`style.md` §3).

---

## 5. Effects & elevation (from `tokens.md` §5 + `style.md` §5)

- Effects collection holds the geometry (shadow x/y/blur/spread, focus-ring spread, blur radii); Semantic Colors' `Elevations/*` group holds the color/opacity. Combine both to build an actual shadow — don't hand-roll a box-shadow value.
- Elevation maps to layering, not decoration:

  | Level | Use for |
  |---|---|
  | 0 | Flat/inline content |
  | 1–2 | Cards, dropdown panels |
  | 3–4 | Popovers, floating toolbars |
  | 5–6 | Modals, anything above the rest of the UI |

---

## 6. Components (from `components.md`)

11 categories exist: Buttons, Toggle, Checkbox, Inputs, Dropdown, Tag & Chips, Progress indicators, Navigation, Cards, Table, Modals, Tooltip & Toast. **Buttons** is the fully-documented reference (`📄 Buttons Docs`) — use its structure (variant showcase → sizing table → color-token table → usage guidance) as the template for the rest as they get built out.

Category-specific build rules:

| Category | Rule |
|---|---|
| Buttons | One Primary per view; Tertiary for low-emphasis/inline; Destructive=true only behind confirmation |
| Toggle | Binary settings only — 3+ mutually exclusive states go to Dropdown or Radiobutton |
| Checkbox / Radiobutton / Check Circle | Checkbox = independent multi-select, Radiobutton = mutually exclusive single-select, Check Circle = card-style selection UI |
| Inputs | Match field style to context — Over line for complex forms, On line for compact layouts, Underline for minimal weight, Phone number/Currency only for that exact data type |
| Dropdown | Checkbox-leading list items for multi-select, icon/avatar/dot-leading for single-select |
| Tag & Chips | Chips = user-controlled/removable, Tags = system-set status (not removable) |
| Progress indicators | Currently chart-style (Doughnut/Velocity), not a linear progress bar — confirm scope before assuming one exists |
| Navigation | SideNav for primary app nav, Tab for switching within one context, Quick filters for narrowing a list without changing the view |
| Cards | Metric Card for dashboard stats, Card row for clickable list-style rows |
| Table | Use the shared Status indicator component for any state column — no ad-hoc colored text |
| Modals | 4 separate templates (Create task/Invite members/Confirmation/Uploadfile), not variants — start from Confirmation and build up for new use cases, don't strip down from Create task |
| Tooltip & Toast | Tooltip = contextual hover/focus hint on one element; Toast = time-limited system feedback, never for anything needing confirmation (use a Modal) |

General rules for any category:
1. Reach for an existing component instance first — don't recreate visuals from scratch.
2. If a needed variant doesn't exist, extend the component's variant set rather than detaching and hand-styling an instance.
3. Any hardcoded fill/font-size/spacing value is a bug — it should be a token.

---

## 7. Iconography & voice (from `style.md` §6–7)

- Icons follow the same color hierarchy as text: `Icons/base/primary` standard, `Icons/base/brand` for active/selected, `Icons/base/inverse` on filled/dark backgrounds.
- Pair icons with a text label wherever meaning isn't universal — status icons in Tags/Toasts always ship with text too.
- Copy: plain language first (this system serves a beginner, ed-tech audience), verb-first sentence-case button labels ("Save changes," not "Changes" or "SAVE CHANGES"), no forced humor in error/empty states.

---

## 8. Accessibility baseline

- Semantic color pairs (e.g. `Text/primary` on `Bg/app bg`) are pre-chosen for WCAG AA in both modes — don't substitute a different pair without re-checking contrast.
- Every interactive component needs a visible focus treatment using `Border/focus` / `Elevations/focus/primary` — not a browser default outline.
- Never convey state through color alone (error/success/disabled) — pair with an icon or text label.

---

## 9. Do / Don't summary (from `style.md` §8)

| Do | Don't |
|---|---|
| Bind Semantic tokens (`Bg/button/primary/default`) | Bind Primitives directly, or hardcode a hex value |
| Use one of the 12 Spacing scale values for any gap/padding | Use an arbitrary pixel value "because it looked right" |
| Reuse an existing component/variant | Detach an instance and hand-style it |
| Map new statuses to the 5 existing meanings | Invent a 6th status color |
| Match elevation to actual layering | Add shadow for visual weight without a layering reason |
| Write verb-first, plain-language copy | Use unexplained jargon or all-caps button labels |
| Treat Modals as 4 templates to duplicate | Treat Modals as one variant-driven component |

---

## 10. Keeping this in sync

`tokens.md`, `components.md`, and `style.md` are the sources of truth — this file should be re-derived from them whenever any of the three changes materially (new token collection counts, new component categories, new brand/voice rules). The drift that prompted the last full doc rebuild (Semantic Colors 59→170, Spacing/Sizing 41→82, undocumented) is exactly what this cross-referencing structure is meant to prevent — if a number here stops matching the corresponding table in `tokens.md` or `components.md`, that's the signal to refresh this file, not the other way around.
