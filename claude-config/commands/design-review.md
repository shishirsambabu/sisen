---
description: Review current UI work against the design system and accessibility bar
argument-hint: [screen or route]
---

Review the UI ($1 if provided, otherwise the current diff) against `docs/DESIGN_SYSTEM.md` and the
accessibility checklist in `docs/TESTING.md`. Use the `design-reviewer` subagent.

Check and report on:

- **Tokens:** no hardcoded colors/spacing; uses semantic tokens and the 8px grid.
- **States:** loading (skeletons, zero layout shift), empty (contextual + action), error
  (recoverable), success/optimistic (with undo where sensible).
- **Motion:** purposeful, correct durations/easing, respects `prefers-reduced-motion`.
- **Composition:** built from existing design-system primitives, not ad-hoc markup.
- **Accessibility (WCAG 2.1 AA):** keyboard operability, visible focus, labels/roles, contrast,
  not color-alone, adequate target sizes, live regions for async updates.
- **Responsive:** mobile → ultrawide.
- **EX:** feels personal/supportive, not administrative.

Output a concise pass/fail list with specific fixes. Flag anything "off-system."
