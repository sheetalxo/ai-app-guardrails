# UI / UX reference

The goal: calm, clear, consistent — the kind of interface where nothing needs explaining. Decide the system first, then build screens from it.

## 1. Define design tokens first (and only use them)
- **Color:** one primary, one accent at most, a neutral scale (7–9 steps), and semantic colors (success/warning/danger/info). Backgrounds mostly neutral; color is for meaning and primary actions. Text/background contrast ≥ 4.5:1 (3:1 for large text). Never use color alone to convey state.
- **Type:** max two font families (one is fine). A fixed scale, e.g. 12 / 14 / 16 / 20 / 24 / 32 / 48. Body 16px, line-height 1.5–1.7, line length 60–75 characters. Real hierarchy: one H1 per page.
- **Spacing:** 4/8px scale only (4, 8, 12, 16, 24, 32, 48, 64). More whitespace than feels necessary — cramped is the #1 reason UIs look amateur.
- **Radius & shadow:** one radius family (e.g. 8px, 12px for cards, full for pills) and 2–3 shadow levels. Consistent everywhere.
- Put tokens in CSS variables / Tailwind theme so the whole app can be re-skinned in one place.

## 2. Layout
- Mobile-first, then enhance for tablet/desktop. Test at 360px, 768px, 1280px. No horizontal scroll.
- A clear visual hierarchy per screen: one primary action, secondary actions visually quieter.
- Use a max content width (≈1100–1200px) and consistent page padding. Align to a grid; left-align text and forms (centering long text hurts readability).
- Navigation: obvious, short labels, current page highlighted. Mobile: bottom bar or simple menu; thumb-reachable.
- Avoid nested cards inside cards inside cards, and walls of equally-weighted boxes.

## 3. Every screen needs these states
- **Loading:** skeletons or spinners (never a blank screen).
- **Empty:** friendly explanation + the action to fix it ("No orders yet — Add your first product").
- **Error:** says what happened and what to do next; keeps the user's input; has retry.
- **Success:** brief confirmation (toast) and a clear next step.
- **Disabled/pending buttons** while submitting to prevent double submits.

## 4. Forms
- Visible labels above inputs (placeholder is not a label). Correct `type`, `autocomplete`, `inputmode`.
- Inline validation on blur with specific messages ("Enter a 10-digit phone number"), not "Invalid input". Mark optional fields rather than required ones when most are required.
- One column, logical order, big tap targets (≥ 44×44px), primary button full-width on mobile.
- Confirm destructive actions; allow undo where possible.

## 5. Components & interaction
- Consistent button set: primary, secondary, ghost, danger. Same height, padding, radius.
- Visible `:focus-visible` ring on all interactive elements; hover and active states; cursor pointer on clickable items.
- Tables on mobile become stacked cards or scroll inside their own container.
- Modals: trap focus, close on Esc, don't use for long flows.
- Motion: 150–250ms ease-out, purposeful only (feedback, transitions). Respect `prefers-reduced-motion`.
- Icons from one library (Lucide/Heroicons), consistent stroke. No emoji as UI icons.
- Images: set width/height, `alt` text, lazy-load, optimized formats.

## 6. Accessibility baseline
- Semantic HTML (`button`, `nav`, `main`, `label`, headings in order) before ARIA.
- Fully keyboard-operable; logical tab order; skip link on content-heavy pages.
- `alt` on images, labels on inputs, `aria-live` for async messages.
- Supports zoom to 200% and system dark mode if dark theme is provided (test contrast in both).

## 7. Copy
- Real, specific text — no lorem ipsum, no "Click here", no "Welcome to our amazing platform". Buttons start with a verb ("Save changes", "Place order").
- Friendly, short sentences; errors never blame the user.
- Match the user's audience and language (e.g. Hinglish/Hindi UI if requested).

## 8. Look-and-feel pitfalls (the "AI-generated" tells)
Avoid: purple-to-blue gradients everywhere, heavy glassmorphism/glow, every element centered, many font weights/sizes, giant rounded cards with drop shadows on everything, emoji sprinkled as decoration, stock hero with vague slogan, dense dashboards with ten equal widgets. Prefer: restrained palette, strong typography, generous spacing, one clear focal point per screen.

## 9. Performance feel
Fast first paint (small bundles, lazy-load below the fold), optimistic UI for quick actions, skeletons over spinners, no layout shift (reserve image/space sizes).

## Quick self-review
Squint at the screen: is there one obvious focal point? Is spacing consistent? Could someone use it one-handed on a phone? Does every list/form handle empty, loading and error?
