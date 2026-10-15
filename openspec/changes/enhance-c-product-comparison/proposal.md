# Proposal

## Why

The "C Product Comparison" section (`sections/customcode-product-comparison.liquid`) currently locks every product block into a fixed image-after-text layout and offers only one static CTA (a section-level button tied to an anchor link). Merchants building comparison/offer pages need per-product layout flexibility and the ability to drop a styled, fully-configurable CTA button anywhere among the callouts without a developer edit.

## What Changes

- Add an `image_first` checkbox setting to the `product` block (default: off, preserving today's text-then-image order). One setting controls both mobile and desktop; layout order is implemented via scoped CSS keyed by `data-block-id`, following the existing pattern used for the `callout` block's `bg_color`.
- Add a new `button` block, visually based on the Content Generator's `button` block and the shared `.c-button` class (`snippets/customcode-styles.liquid`), but with merchant-configurable link, text, font color, and background color (overriding the shared defaults via scoped, block-id-keyed styles).
- Render `button` blocks in the same zone as `callout` blocks (after the product grid), preserving the relative admin order between `callout` and `button` blocks. The `product` block's dedicated rendering loop (the grid) and the `letter` block's separate right-column rendering are unchanged — because products are only ever rendered from their own loop, a `button` or `callout` block can never structurally land in the middle of the product grid.

## Capabilities

### New Capabilities
- `product-comparison-section`: Rendering behavior of the "C Product Comparison" section — its block types (`product`, `callout`, `button`, `letter`), their settings, and how they are composed into the final layout.

### Modified Capabilities
(none — this is the first spec for this section)

## Impact

- `sections/customcode-product-comparison.liquid`: new `button` block schema + rendering branch; new `image_first` setting on the `product` block; scoped `{% style %}` additions for per-block layout order and button colors.
- `snippets/customcode-styles.liquid`: no changes required to the shared `.c-button` base rule; per-block color overrides are scoped in the section file itself (consistent with how `callout.bg_color` is already handled there).
- No changes to `letter` block rendering or the section-level `button_link`/`button_text` settings (left as-is).
