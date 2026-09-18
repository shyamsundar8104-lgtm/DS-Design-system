# DS-Design-system
Design system documentation (guidelines, tokens, style, and components) for building the SaaS application in Figma file "DS-Design-system1.0."
# Design System 1.0

Documentation for the design system used to build this product's SaaS application — sourced from the [Figma file](https://www.figma.com/design/G476EIZkToaQPfb49fC4wY/Design-system1.0).

## Start here

👉 **[guidelines.md](./guidelines.md)** — the playbook for building a SaaS application with this system. Start here; it links out to everything else as needed.

## Structure

| File | What it covers |
|---|---|
| [guidelines.md](./guidelines.md) | How to assemble screens and flows (auth, dashboard, settings, tables, forms, etc.) using the components, tokens, and style rules below |
| [style.md](./style.md) | The visual language built on top of tokens — elevation, color usage, typography pairing, state logic |
| [tokens.md](./tokens.md) | Raw design tokens — color, type, spacing, radius, shadow, opacity |
| [components/components.md](./components/components.md) | Index of all components, with documentation status |
| [components/dropdown.md](./components/dropdown.md) | Dropdown component — anatomy, variants, states, tokens, dev reference |

## Status

Only the **Dropdown** component has been fully documented against the Figma file so far. `tokens.md` and `style.md` currently reflect only what's been verified from Dropdown — treat values there as a starting foundation, not a complete system, until more components are pulled in. See [components/components.md](./components/components.md) for per-component status.

## Contributing

When documenting a new component:
1. Add its tokens to `tokens.md` (reuse existing tokens where the values match — don't create duplicates).
2. Add any new visual rules to `style.md`.
3. Add a new file under `components/` following the structure of `dropdown.md`.
4. Update its status in `components/components.md`.
5. Update `guidelines.md` if the component introduces a new screen pattern.
