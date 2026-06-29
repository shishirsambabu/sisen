# Design System

> The product should feel like software from 2030: premium, minimal, fast, intelligent.
> Inspiration: Linear, Stripe Dashboard, Vercel, Notion, Apple, Arc, Figma, Cursor, Framer.
> Avoid: Bootstrap look, legacy LMS interfaces, card overload, clutter.

This is a **cohesive system with design tokens and reusable components** — not ad-hoc styling.
Every screen is built from the system.

## 1. Foundations

### Spacing — 8px grid
All spacing, sizing, and layout snap to a 4/8px scale: `4, 8, 12, 16, 24, 32, 48, 64, 96`.
Encode as Tailwind spacing tokens. No magic pixel values.

### Typography
- Display/UI sans (e.g. Inter / Geist); mono for code (e.g. JetBrains Mono / Geist Mono).
- A constrained type scale (e.g. `xs 12 / sm 14 / base 16 / lg 18 / xl 20 / 2xl 24 / 3xl 30 /
  4xl 36 / 5xl 48`) with consistent line-height and tracking. Premium, legible, generous spacing.

### Color & theming
- **Theme engine** with **light + dark** modes and per-tenant branding (primary color, logo).
- Semantic tokens only in components: `--bg`, `--surface`, `--surface-elevated`, `--border`,
  `--text`, `--text-muted`, `--primary`, `--primary-foreground`, `--success`, `--warning`,
  `--danger`, `--ring`. Map tokens → Tailwind theme; never hardcode hex in components.
- Glassmorphism, soft shadows, and subtle gradients used **purposefully** (overlays, command
  palette, elevated surfaces) — not everywhere.

### Elevation & radius
- A small shadow scale (`sm/md/lg/xl`) for soft, layered depth.
- Consistent radius scale (`sm 6 / md 10 / lg 14 / xl 20 / full`).

### Iconography
- One premium icon set (e.g. Lucide), consistent stroke + size, optically aligned.

## 2. Motion

Motion communicates state and purpose; it is never decorative noise.
- Page/section transitions are instant-feeling; shared layout transitions for continuity.
- Standard durations: `120ms` micro, `200ms` standard, `320ms` emphasis; easing `ease-out` for
  enters, `ease-in-out` for moves. Respect `prefers-reduced-motion`.
- Micro-interactions on hover/press/focus; animated charts; smooth drag-and-drop.

## 3. Required states (every screen, every list)

- **Loading:** skeletons matching final layout. **Zero layout shift.**
- **Empty:** contextual, helpful empty states with a clear primary action.
- **Error:** friendly, recoverable, with retry.
- **Success / optimistic:** optimistic updates with reconciliation + undo where it makes sense.

## 4. Core component inventory

Built on shadcn/ui primitives, themed via tokens, all accessible:

- App shell: sidebar, top bar, breadcrumbs, responsive layout (mobile → ultrawide).
- **Command palette** (global, `⌘K`) and **Spotlight search**.
- Context menus + right-click support; keyboard shortcuts surface + help dialog.
- Data: tables (sortable, paginated, virtualized), data viz/charts, stat cards, heatmaps.
- Inputs: forms (RHF + Zod), combobox, date/time, file upload (drag-drop, progress), rich text.
- Feedback: toasts, dialogs, sheets, popovers, tooltips, progress, badges.
- **Inline AI actions & AI copilots** as first-class components (e.g. "✨ improve", "summarize",
  "generate") with streaming output and clear affordances.
- Onboarding: immersive, progressive-disclosure flows.

## 5. Accessibility — WCAG 2.1 AA (minimum)

- Full keyboard operability; visible focus rings (`--ring`); logical tab order.
- Color contrast ≥ AA; never rely on color alone.
- Proper semantics/ARIA; labelled controls; live regions for async updates.
- Respect reduced motion; target sizes adequate for touch.
- Run the accessibility checklist (see `TESTING.md`) on every new screen.

## 6. Employee Experience (EX) principles

Every learner interaction should feel personal and supportive, not administrative:
personalized home, daily goals, AI coaching, motivation nudges, streaks, career/skill
visualizations, recognition, and a **wellness-aware, non-intrusive** reminder cadence.

## 7. Definition of "on-system"

A UI change is on-system only if it uses tokens (no hardcoded colors/spacing), composes existing
primitives, includes all four states, is keyboard-accessible, and meets AA contrast.
