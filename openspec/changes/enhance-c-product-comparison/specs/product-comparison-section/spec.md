# Spec Delta

## Purpose

Defines the rendering behavior of the "C Product Comparison" section: its block types (`product`, `callout`, `button`, `letter`), their merchant-configurable settings, and how those blocks compose into the final section layout.

## ADDED Requirements

### Requirement: Product block layout order toggle
Each `product` block SHALL expose a single checkbox setting that controls whether that block's image renders before or after its text content (title, description, price, blurb). The setting SHALL apply identically on mobile and desktop — there is no separate breakpoint control. When the setting is off, the block SHALL render text before image, matching current default behavior.

#### Scenario: Default order is text first
- **WHEN** a merchant adds a `product` block and leaves the image-first setting unchecked
- **THEN** the block renders its text content before its image, identically on mobile and desktop

#### Scenario: Image-first order enabled
- **WHEN** a merchant checks the image-first setting on a `product` block
- **THEN** that block's image renders before its text content, identically on mobile and desktop, while other `product` blocks in the same section are unaffected

### Requirement: Configurable button block
The section SHALL support a `button` block type that renders a single call-to-action link styled like the section's shared button appearance. The block SHALL expose merchant-configurable settings for link destination, button text, font color, and background color. When a color setting is left unset, the button SHALL fall back to the shared default button colors.

#### Scenario: Button renders with merchant-configured values
- **WHEN** a merchant adds a `button` block and sets a link, text, font color, and background color
- **THEN** the rendered button uses that link, text, and colors

#### Scenario: Button falls back to default colors
- **WHEN** a merchant adds a `button` block and leaves font color and background color unset
- **THEN** the rendered button uses the section's shared default button colors

### Requirement: Button and callout relative ordering
`button` blocks SHALL render in the same layout zone as `callout` blocks (after the full product grid), preserving the relative order in which `button` and `callout` blocks were arranged by the merchant. `product` blocks SHALL always render together as a single contiguous grid, independent of where `button` or `callout` blocks are positioned among the section's blocks — a `button` or `callout` block can never split the product grid.

#### Scenario: Button placed between two callouts keeps its position
- **WHEN** a merchant arranges blocks as callout A, button, callout B
- **THEN** the rendered section shows callout A, then the button, then callout B, in that order, after the product grid

#### Scenario: Product grid is never split by button or callout blocks
- **WHEN** a merchant arranges blocks with `product` blocks interleaved with `button` or `callout` blocks in the admin block list
- **THEN** all `product` blocks still render together as one contiguous grid, and all `button`/`callout` blocks render together after that grid in their relative order

### Requirement: Letter block remains unaffected
The `letter` block SHALL continue to render in its own separate column, independent of `product`, `callout`, and `button` block placement or ordering.

#### Scenario: Letter block unaffected by new block types
- **WHEN** a section contains `product`, `callout`, `button`, and `letter` blocks in any order
- **THEN** the `letter` block still renders in its own separate column, unchanged by the presence or position of the new `button` block
