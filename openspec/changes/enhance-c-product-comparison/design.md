# Design

## Context

`sections/customcode-product-comparison.liquid` currently renders `section.blocks` with two separate, type-filtered `{% for %}` loops inside `.left-container`: one collects every `product` block into `.grid-container`, the other collects every `callout` block into a sequence of `.callout-container` links rendered after the grid. A third loop renders every `letter` block into `.right-container`. Because each loop filters by `block.type` independently, a block's position in `section.blocks` only matters *within* its own type's loop — it already cannot "split" another type's loop. Shared button styling (`.c-button`) and the per-block scoped-CSS pattern (`callout.bg_color` keyed by `data-block-id` in the section's `{% style %}` block) already exist and are the templates for this change. See proposal.md for motivation.

## Goals / Non-Goals

**Goals:**
- Add a per-`product`-block layout toggle (image-first vs. text-first) with no new markup structure beyond reordering the two existing children of `.grid-item`.
- Add a `button` block that renders alongside `callout` blocks, in their shared relative admin order, with configurable link/text/font color/background color.
- Reuse existing patterns (scoped `data-block-id` CSS, shared `.c-button` class) rather than introducing a new styling mechanism.

**Non-Goals:**
- No change to how `product` or `letter` blocks are grouped/rendered structurally.
- No new validation or merchant-facing warning system to "prevent" illegal block placement — the architecture itself makes product-grid-splitting impossible, so no defensive logic is needed.
- No independent mobile/desktop control for the image-first toggle.
- No changes to the section-level `button_link`/`button_text` settings (the static anchor-link button above the callouts stays as-is).

## Decisions

**Image-first toggle via scoped CSS `order`, not markup reordering.**
Add `image_first` (checkbox, default `false`) to the `product` block schema. Rather than branching the Liquid markup to swap which element prints first, apply the existing scoped-CSS-by-`data-block-id` pattern already used for `callout.bg_color`: give `.info-container` and the product `img` explicit `order` values (or flex order swap) inside a `c-product-comparison .grid-item[data-block-id="{{ block.id }}"]` rule when `block.settings.image_first` is true. This matches how the file already scopes per-block style (callout bg-color) and avoids duplicating the info-container/image markup in two branches. Requires adding `data-block-id="{{ block.id }}"` to `.grid-item` (not currently present).
*Alternative considered:* an `{% if %}/{% else %}` branch duplicating the two children in swapped order — rejected as needless duplication when a single CSS rule does the same job with less markup to keep in sync.

**Button block styling via scoped per-block CSS, not inline `style=`.**
The `button` block's font color and background color are applied the same way as `callout.bg_color`: a rule in the section's `{% style %}` block scoped to `[data-block-id="{{ block.id }}"]`, only emitting the `color`/`background-color` declarations when the corresponding setting is set (so an unset color falls through to the shared `.c-button` default from `customcode-styles.liquid` instead of forcing a value). The button reuses the `.c-button` class for shape/padding/base behavior, same as Content Generator's `button` block.
*Alternative considered:* inline `style="color: {{ ... }}; background-color: {{ ... }}"` — rejected because an unset setting would render an empty-but-present declaration in some Liquid output patterns and is inconsistent with the file's existing convention of scoped `{% style %}` blocks for per-block color.

**Button/callout ordering via a single merged loop, not a sequencing rewrite.**
Replace the dedicated `{% for block %}{% if block.type == 'callout' %}` loop with one loop over `section.blocks` that uses `{% case block.type %}` (`when 'callout'`, `when 'button'`) to render whichever matches, in the order they naturally appear in `section.blocks`. This is the minimal change that satisfies "button and callout preserve relative order with each other" — it does not touch the separate `product` loop or the separate `letter` loop, so those remain exactly as architecturally isolated as they are today (confirmed with the user: no full sequence-based rewrite needed, `product` keeps its own dedicated handling).

## Risks / Trade-offs

- **Scoped-CSS color fallback correctness** → If a Liquid `{% if block.settings.font_color != blank %}` guard is omitted, an unset color setting could emit `color: ;`, breaking inheritance from `.c-button`. Mitigation: always guard each declaration independently, matching the existing `callout` bg-color block's style (which is unconditional today because `bg_color` has no "unset" fallback requirement — this change introduces the first conditional per-declaration guard in this file, so it needs explicit care during implementation).
- **`.grid-item` gains `data-block-id`** → This is a small markup addition with no behavioral side effects, but it's worth noting since `.grid-item` currently has no per-instance identity hook at all.

## Migration Plan

No data migration. This is additive to section schema (new block type, new setting) plus a rendering refactor of one loop. Existing sections using this section file are unaffected until a merchant adds a `button` block or checks `image_first` — current behavior is the default state for both.
