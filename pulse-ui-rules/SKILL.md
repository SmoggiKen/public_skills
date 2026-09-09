---
name: pulse-ui-rules
description: Use when designing or changing Pulse admin, dashboard builder, runtime dashboard, modal, popover, or home/index UI behavior and styling.
---

# Pulse UI Rules

Use this skill for UI work in the Pulse app, especially admin screens, dashboard builder panels, chart/table editing controls, runtime dashboards, modals, overlays, and home/index surfaces. These are project-specific design preferences, not general rules for unrelated repositories.

## Design Direction

Favor clear, high-signal interfaces over generic admin-form sprawl. Use spacing and grouping to create hierarchy before adding visual chrome. Keep controls readable, compact, calm, and deliberate.

## Panel Structure

- Use sectioned panels instead of long flat lists of controls.
- Put the primary enable/disable toggle first when a panel has one.
- Group related settings under short section titles such as `Labels`, `Timeline`, `Sort`, and `Drill path`.
- Hide sections that are not relevant to the current object, field type, or chart type.
- Prefer progressive disclosure over showing every possible option at once.
- In chart edit tabs, treat `Colours / Targets / Icons`, `X axis`, `Y axis`, `Y2 axis`, `Labels`, `Trends`, `General`, and `Drill path` as one family and keep panel language aligned.

## Layout

- Size controls to intent. Do not stretch controls across an entire panel by default.
- For compact settings panels, use content-width layouts rather than full-width equal columns.
- Standard compact field width is `150px`.
- Gap between fields inside a section is `20px`.
- Gap between major sections in the same panel is `60px`.
- Keep section titles left-aligned and visually separated from their controls.
- Use divider lines to separate major sections when a panel contains multiple groups.

## Motion

- Prefer short structural animations over abrupt show/hide changes.
- Animate section collapse/expand when driven by a toggle.
- Animate neighboring panels shifting into freed space when a section is hidden.
- Keep motion subtle and fast.
- Do not stack multiple movement transforms when the container or grid animation already provides the movement.

## Overlays

- Popovers, hover cards, and anchored overlays should default to the direction least likely to leave the viewport.
- If an anchor commonly appears near the top of the page, prefer opening downward.
- Give anchored overlays a viewport-safe max width instead of assuming the container width is always safe.

## Toggles

- Use switch-style toggles for boolean settings in the builder/admin UI.
- Checked state must use the active theme accent color, not a hardcoded color.
- Put the toggle label inline to the right with consistent spacing.
- Give toggle controls enough surrounding space to avoid collisions in dense grids.
- When replacing native checkboxes with switches, revisit the surrounding layout because switches are wider.

## Conditional Visibility

- Only show controls valid for the current context.
- Date-only controls should only appear for date-like fields.
- `Timeline` options should only appear when the active X-axis field is date-like.
- If a controlling toggle is off, hide dependent sections and rebalance the layout.

## Inputs

- Use plain numeric inputs for editable numbers where spinner behavior, clearing, and direct editing matter.
- Avoid decorative suffixes when they interfere with input behavior.
- If units are necessary, prefer labels or helper text unless the suffix can be added without degrading editing.
- Inputs in compact panels should align visually and share consistent heights.

## Colour Controls

Use one consistent colour control pattern across the builder:

- editable hex text input
- linked colour swatch
- swatch opens the native colour picker

The text field and swatch should feel like one grouped control. The swatch should update live while typing a valid value. Accept both `#RRGGBB` and `0xRRGGBB` input formats where useful.

Once this control is approved in one builder panel, use it instead of plain native colour inputs in equivalent settings panels. This applies to target thresholds, legend colours, icon/target settings, axis labels, and other dashboard-builder colour fields.

## Builder Behavior

- Adapt based on the selected field type, chart type, and enabled options.
- When a setting becomes irrelevant, hide it instead of leaving it visible but meaningless.
- Preserve the feeling that advanced features are available without making the default experience heavy.

## Consistency

- The same interaction should use the same visual treatment everywhere.
- Once a panel pattern is approved in one analogous area, apply it to equivalent panels by default.
- If an `X axis` pattern is accepted, treat that as the baseline for `Y axis`, `Y2 axis`, and similar builder panels unless there is a clear functional reason to differ.
- If the user corrects a UI behavior or layout decision, update the source UI rules document in `/Users/admin/pulse/AI_UI_RULES.md` when the user asks for documentation maintenance.
- Once a control pattern is accepted in one area, standardize it across chart settings, table settings, threshold/target configuration, and dashboard formatting controls.
- Avoid one-off UI patterns unless there is a strong functional reason.

## Theme Compatibility

Any new UI treatment must be checked in both light and dark themes before it is considered complete.

- Do not rely on light-theme colors, pale status fills, or low-contrast neutrals without dark-theme equivalents.
- Status treatments such as overridden, warning, success, and disabled states must preserve readable contrast in both themes.
- If a component adds custom background, border, or text colors, add explicit dark-theme styling at the same time.
- Inline help, pills, banners, and highlighted rows must remain readable against the active theme surface and must not wash out in dark mode.
- Theme compatibility applies to admin screens, builder panels, runtime dashboards, modals, popovers, and inline editing states.

## Home And Index Surfaces

Landing and home/index surfaces may use a stronger visual metaphor than admin and builder panels, provided interaction remains clear.

- Prefer grouped dashboard collections over one undifferentiated grid.
- Role or category groups on the home screen should be visibly bounded as their own sections, not just implied by a heading.
- Prefer a stable grid of role/category sections over highly variable section widths.
- On desktop, role/category sections should usually use a disciplined board grid before adding variation inside cards.
- Dashboard home cards may use controlled irregularity such as light rotation, layered paper stacks, pins, and tactile shadows.
- Irregularity should be systematic, not random on each render. Use stable variation by index or data, not per-frame randomness.
- Irregularity must stay within section bounds. Decorative offsets should stay subtle enough that cards do not bleed off-screen or break role grouping.
- Decorative devices only stay if they add clear value. Remove elements that do not improve hierarchy or interaction.
- Home cards should feel placed on top of their role/category board. They may float slightly beyond the internal board area, but should not be clipped or break the overall group structure.
- Preserve clear click targets, readable titles, and predictable hover behavior.
- Floating top controls are acceptable on home/index pages when compact and readable.
- Desktop can lean into a tactile or editorial layout; mobile should simplify to a clean stacked layout with reduced ornament and no awkward overlap.
