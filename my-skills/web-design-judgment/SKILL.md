---
name: "web-design-judgment"
description: "Use when designing, building, or reviewing web UI/frontend pages — picking layouts, colors, typography, or animations — so output avoids generic 'AI-made' tells and follows expert motion/interaction craft."
---

# Web Design Judgment

Two combined bodies of practice: **Emil Kowalski's animation/interaction craft** (the how of motion) and **anti-generic-AI-design rules** (the what to avoid so a page doesn't read as AI-templated). Apply both whenever producing or reviewing real web UI — landing pages, dashboards, marketing sites, app screens.

Core stance: taste is trained, not innate — these are concrete, checkable rules, not vibes. Read the brief (audience, vibe signals, references) before defaulting to a template. Beauty and polish are competitive leverage, not decoration.

## Three calibration dials (before you start)

Infer these from the brief instead of defaulting blindly. Baseline if nothing is specified: **variance 8, motion 6, density 4** (out of 10) — but a formal B2B dashboard brief should pull all three down; a playful consumer/creative brief should push variance and motion up.

- **DESIGN_VARIANCE**: 1 = perfectly symmetrical/centered, 10 = chaotic asymmetry.
- **MOTION_INTENSITY**: 1 = static CSS only, 10 = complex choreography (GSAP-grade).
- **VISUAL_DENSITY**: 1 = art-gallery spacing, 10 = cockpit-dense data.

## Banned "AI tells" — check before shipping

**Typography**: don't default to serif (especially Fraunces / Instrument Serif) unless the brand explicitly calls for editorial/luxury. Italic display type needs `leading-[1.1]` minimum so descenders don't clip.

**Color**: no default AI purple/blue glow aesthetic. No default warm beige+brass+espresso palette for premium-consumer briefs unless asked. Max one accent color per project, saturation under 80%.

**Layout**: no centered hero when variance > 4. No three-equal-feature-cards layout. Max 1 "eyebrow" label per 3 sections. No more than 2 consecutive image+text zigzag rows. No section-number eyebrows (001, 02...). No layout family repeated twice on one page. Bento grids must have exact cell count matching real content — no empty filler cells. Lists longer than 5 items need a card grid, tabs, or carousel — not a bare `<ul>`.

**Copy**: zero em-dashes (—) anywhere — headlines, eyebrows, pills, body, quotes, captions, buttons, alt text. No placeholder names ("John Doe", "Acme", "Nexus"). No fake-precise stats without real data (99.99%, 1234567). No filler verbs ("Elevate", "Seamless", "Unleash"). No duplicate CTA intents on one page.

**Images**: never fake a screenshot with divs. Avoid hand-rolled decorative SVGs when a real asset/icon set is available. No version/build labels in a hero unless it's genuinely a product-launch page.

## Hero + layout hard rules

Hero must fit the initial viewport without scrolling: headline max 2 lines desktop, subtext max ~20 words and max 3–4 lines, CTA visible without scrolling. Hero top padding capped around `pt-24` (~6rem) at desktop. Hero text stack ≤ 4 elements. Nav must render on one line at desktop. Use `min-h-[100dvh]` instead of `h-screen` for viewport stability on mobile.

Prefer an official design system package over hand-rolled CSS when one exists for the stack (Material Web, Fluent UI, Carbon, Radix Themes, shadcn/ui, Polaris, GOV.UK Frontend, USWDS). Never mix two design systems in one project.

## Motion: the decision framework

**Should this animate at all?** Frequency test — how often will a user see it:
- 100+ times/day (keyboard shortcuts): never animate.
- Tens of times/day (hover, nav): remove or drastically reduce.
- Occasional (modals, drawers, toasts): standard animation is fine.
- Rare (onboarding, celebrations, empty states): delight animation is appropriate.

**Does it have a real purpose?** Valid reasons: spatial consistency (exit path mirrors entry), state indication, explaining a feature, confirming input was received, or smoothing an otherwise jarring DOM change. "It looks cool" is not a valid reason if the user will see it often. If a motion dial > 4 was set, the page must actually show that much motion — don't claim it and not deliver it.

**Easing** — pick by what's happening, not by default:
- Entering → `ease-out`.
- Moving/morphing on screen → `ease-in-out`.
- Hover/color change → `ease`.
- Constant motion (marquees, progress bars) → `linear`.
- Never `ease-in` for UI — it delays the initial movement, exactly when the user is watching most closely, so it *feels* slower than `ease-out` at the same duration.
- Use custom cubic-bezier curves, not bare browser defaults, e.g. `cubic-bezier(0.23, 1, 0.32, 1)` for ease-out, `cubic-bezier(0.77, 0, 0.175, 1)` for ease-in-out.

**Duration** — UI animation stays under 300ms: button press 100–160ms, tooltips/small popovers 125–200ms, dropdowns/selects 150–250ms, modals/drawers 200–500ms. Marketing/explanatory animation can run longer. Make exit faster than enter (e.g. enter 2s deliberate hold, release/exit 200ms snappy) — slow where the user is deciding, fast where the system responds.

**Springs**: use for drag-with-momentum, "alive" elements, and interruptible gestures — not for most ordinary UI. Keep bounce subtle (0.1–0.3), e.g. `{ type: "spring", duration: 0.5, bounce: 0.2 }`. Springs preserve velocity on interruption; CSS duration-based animation restarts from zero.

**Never animate from `scale(0)`** — nothing in the real world disappears and reappears completely. Start from `scale(0.95)` + `opacity: 0` instead.

**Stagger** multi-element entries by 30–80ms between items; never let stagger block interaction while it plays.

**Blur bridges bad crossfades**: if a crossfade still looks wrong after timing adjustments, add `filter: blur(2px)` (cap under 20px — expensive in Safari) so perception reads it as one object transforming instead of two swapping.

**Motivated only, always justified** before adding any animation, ask "what does this communicate?" — hierarchy, storytelling, feedback, or a state transition are valid; "it looked cool" is not.

## Performance and correctness rules

- Only animate `transform` and `opacity` — never `top`, `left`, `width`, `height`, `padding`, `margin` (these force layout+paint, not just composite).
- Use CSS transitions (not keyframes) for anything that can be interrupted mid-animation (toasts, state toggles) — keyframes restart from scratch, transitions retarget smoothly.
- Don't drive animation off a CSS custom property changed every frame (`--x`) — it forces a style recalc on every child. Set `transform` directly instead.
- In Framer Motion, shorthand props (`x`, `y`, `scale`) are NOT hardware-accelerated — use `animate={{ transform: "translateX(100px)" }}` for GPU acceleration.
- No `window.addEventListener("scroll")` for scroll-linked effects (janky) — use `useScroll`/`useMotionValue`, GSAP ScrollTrigger, or IntersectionObserver instead.
- Prefer percentage-based `translate()` (e.g. `translateY(100%)`) over hardcoded pixels so it adapts to content size.
- Use `transform-origin` matching the trigger location for popovers (origin-aware); keep modals centered.

## Accessibility

- Respect `prefers-reduced-motion`: reduce and simplify motion, don't necessarily remove all of it — keep opacity/color transitions that aid comprehension, drop transform-based movement. Required whenever motion intensity > 3.
- Gate hover effects behind `@media (hover: hover) and (pointer: fine)` so touch devices don't get false-positive hover states from a tap.
- Button/CTA contrast must clear WCAG AA (4.5:1); form inputs must pass AA against their section background. CTA text must never wrap to 2 lines at desktop.
- If the page is consumer-facing, dark mode is expected — design and check both modes before calling it done.
- Target LCP < 2.5s; hero image should be priority-loaded.

## Redesign / existing-brand work

First detect the mode: greenfield, preserve-existing-brand, or full overhaul — don't guess. Audit before touching code: document existing brand tokens, information architecture, and patterns already in place. Never silently change URL structure, nav labels, or form field names. When modernizing, work in this order: typography → spacing → color → motion → key sections → full replacement — don't jump straight to a full rebuild.

## Pre-flight checklist (run before calling any design output done)

Zero em-dashes · one consistent page theme (no random section inversion) · one consistent accent color · consistent corner radius · button and form contrast pass AA · no CTA text wrapping · serif used only if justified · no default premium-beige palette · italic descenders have clearance · hero fits the initial viewport with capped top padding and ≤4 text elements · eyebrow count ≤ ceil(sections/3) · no 3+ consecutive zigzag rows · no duplicate CTA intents · logo walls use real SVGs, not placeholders · bento grid has exact cell count and visual variety · copy audited for filler/placeholder text · every animation is motivated and justified · at most one marquee on the page · nav renders on one line · section layouts vary down the page · long lists use the right component (grid/tabs/carousel, not a bare list) · images are real, not faked · mobile collapse behavior is explicit · reduced motion is handled · dark mode tested if consumer-facing · re-scan for any AI-design tell before declaring done.