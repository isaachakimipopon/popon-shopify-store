# Tasks

## 1. Product block image-first toggle

- [x] 1.1 Add `image_first` checkbox setting (default `false`) to the `product` block schema in `sections/customcode-product-comparison.liquid`, and verify via `shopify theme check` that the schema parses with no new errors.
- [x] 1.2 Add `data-block-id="{{ block.id }}"` to the `.grid-item` element in the `product` rendering loop, and verify by inspecting rendered HTML (theme dev preview) that each grid item carries its block's id.
- [x] 1.3 In the section's `{% style %}` block, add a scoped rule per product block (`c-product-comparison .grid-item[data-block-id="{{ block.id }}"]`) that reorders `.info-container` and the product `img` when `block.settings.image_first` is true, leaving default order unchanged when false. Verify in the theme editor: a product block with the setting off shows text-then-image (unchanged from today), and a product block with the setting on shows image-then-text, on both a mobile viewport width and a desktop viewport width.

## 2. Configurable button block

- [x] 2.1 Add a `button` block type to the section schema with settings: `button_link` (url), `button_text` (text), `font_color` (color, no default), `background_color` (color, no default). Verify via `shopify theme check` that the schema parses with no new errors.
- [x] 2.2 Render the `button` block using the shared `.c-button` class (matching Content Generator's `button` block markup pattern) with `data-block-id="{{ block.id }}"`. Verify by adding a `button` block in the theme editor and confirming it renders as a link styled like other `.c-button` instances in the section.
- [x] 2.3 In the section's `{% style %}` block, add a scoped rule per button block that sets `color` only when `font_color` is set and `background-color` only when `background_color` is set (each declaration independently guarded with `{% if ... != blank %}`), so an unset color falls back to the shared `.c-button` default. Verify in the theme editor: a button with both colors set renders those colors; a button with both left unset renders the shared default `.c-button` colors unchanged.

## 3. Button/callout relative ordering

- [x] 3.1 Replace the dedicated `{% for block %}{% if block.type == 'callout' %}` loop (after the product grid) with a single loop over `section.blocks` using `{% case block.type %}` with `when 'callout'` and `when 'button'` branches, leaving the `product` grid loop and the `letter` loop untouched. Verify by arranging blocks in the theme editor as callout, button, callout and confirming the rendered order after the product grid matches: callout, button, callout.
- [ ] 3.2 Verify the product grid cannot be split: arrange blocks in the admin as product, callout, product, button, product and confirm the rendered output still shows one contiguous grid of all three products, followed by the callout then the button in their relative order.

## 4. Integration check

- [ ] 4.1 Load a page using the "C Product Comparison" section in the theme editor/dev preview with at least one `product` block (image-first on), one `product` block (image-first off), two `callout` blocks, one `button` block, and one `letter` block arranged in mixed order. Verify the full section renders with no console/Liquid errors, the `letter` block still renders in its separate right column, and `shopify theme check` reports no new errors or warnings introduced by this change.
