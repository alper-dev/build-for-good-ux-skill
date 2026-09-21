---
name: build-for-good-ux
description: Audit, build, or refactor frontend UI components, pages, forms, and interactive flows to implement complete UX states (loading, success, error, empty), resilient async degradation, accessible button interactions, error recovery, Fitts's Law mobile touch targets, and choice simplification. Use when creating or reviewing user-facing interfaces, form validations, multi-section dashboards, checkout flows, or gesture-sensitive mobile screens.
---

# Build For Good UX

Based on Katherine Gilligan's public "Build For Good UX" series ([@synsation_](https://instagram.com/synsation_)). Use this as an implementation checklist for UI work, not as transcript notes.

## Core Principle

Good UI is how an app looks. Good UX is whether users understand what to do, what happened, what failed, and how to recover. AI-generated UI often covers only the happy path. Build the full experience.

Every screen needs these states:

| State | Required UX |
|---|---|
| Loading | Shows progress appropriate to wait time and scope |
| Success | Confirms the user's action worked |
| Error | Explains what happened, why, and next action |
| Empty | Explains why nothing is there and how to start or continue |

Good UX builds trust. Bad UX creates uncertainty, extra steps, and friction. Users leave and blame the product, not themselves.

## Required Workflow

When building or reviewing a UI, do this before considering the work complete:

1. Identify every user action and async data source.
2. Define loading, success, error, and empty states for each relevant screen or section.
3. Pick the loader based on scope and duration; place loaders inside action buttons when scoped to that trigger.
4. Place errors next to the thing that caused them unless the issue blocks the whole flow.
5. Ensure every action gives visible or tactile feedback across all button states (default, hover, focus, pressed, loading, disabled; see [Button States And Accessibility](#button-states-and-accessibility)).
6. Decide whether to keep submit enabled with focused error jump instead of disabled button traps.
7. Make each page section resilient when other sections load slowly or fail.
8. Apply Tesler's Law: absorb complexity in code and system design rather than offloading it to users.
9. Use familiar patterns for the user's device, locale, and audience (Jakob's Law).
10. Respect Fitts's Law: optimize tap target sizes, invisible hit padding, thumb reach, and resolve scroll-vs-tap gesture conflicts.
11. Reduce choice overload with grouping, curation, filtering, or progressive disclosure without hiding core discoverability.
12. Verify the final checklist at the bottom of this file.

## Loading States

Missing loaders make users think the app is broken. A blank screen with no response can lose users in 2-3 seconds. Bad loaders can also make the app feel slower.

### Choose Loader By Context

| Pattern | Use When | Avoid When |
|---|---|---|
| Skeleton screen | Whole page or large content section loads | Small contained action |
| Progress bar | Duration or progress is knowable | Unknown background wait |
| Inline spinner | Button or small section is working | Whole page load |
| Optimistic UI | Action is very likely to succeed | Failure would be costly or confusing |
| No loader | Work finishes under 1 second | Delay may exceed 1 second |

### Timing Rules

| Duration | UX Rule |
|---|---|
| Under 1 second | Show result, no loader. Spinner flash feels slower. |
| 1-2 seconds | Plain spinner is enough. |
| 2-5 seconds | Spinner still works. |
| 5-10 seconds | Add text. Prefer changing text like "Connecting..." then "Almost there...". |
| Over 10 seconds | Replace looped spinner with progress bar or step indicator. |
| On failure | Show error as soon as possible. Do not make users wait then fail. |

### Skeleton Rules

- Show page structure first as gray placeholder outlines.
- Match final layout closely so users start processing structure before data arrives.
- Use skeletons for feeds, dashboards, profiles, and content-heavy pages.
- Use inline spinners for buttons or small parts where skeletons do not fit.

### Optimistic UI Rules

- Update UI immediately when action is likely to succeed, like a like/favorite/toggle.
- Do not wait for server confirmation before showing the expected result.
- If the server fails, roll back clearly and gracefully.
- Do not use optimistic UI for payments, destructive actions, bookings, or anything where false success creates risk.

## Error States

Errors must reduce uncertainty. Never leave users wondering whether an action worked.

### Error Message Formula

Good error messages include all three:

1. What happened.
2. Why it happened, in user language.
3. What the user can do next.

Bad:

```text
Something went wrong.
```

Good:

```text
Your payment didn't go through. Your card was declined. Check your card details or try a different payment method.
```

### Error Rules

- Never dump database, backend, stack trace, or raw exception details into UI.
- Never silently fail. A clicked button must produce feedback.
- Avoid vague messages when user needs certainty.
- Always provide recovery: retry, edit, request access, update payment, or contact support.
- Show errors immediately when known. Do not hide failure behind long loading.

### Error Placement

| Placement | Use When | Rules |
|---|---|---|
| Inline | Field-specific errors, button action failure, most forms | Closest to the problem. Use red border plus message. |
| Toast | Non-critical, recoverable status | Auto-dismiss only if user can miss it without harm. Example: "Couldn't connect, retrying..." |
| Modal | Critical blocker requiring user action | Blocks flow. Must include clear next action. Use sparingly. |

If blocking the user with a modal, provide a way forward.

## Form UX

Forms create friction. Reduce effort, uncertainty, and rework.

### Form Rules

1. Apply the disabled-button decision rule below; default to keeping submit enabled.
2. Mark required fields clearly so users are never guessing why submit cannot proceed.
3. Validate inline when users leave a field, not only after submit.
4. Keep validation messages near the field, not at the top of the page.
5. Show character counts for limited fields.
6. Pre-fill known values, such as logged-in user's email.
7. Show password requirements while typing and check them off live.
8. Accept forgiving formats, such as phone numbers with spaces, dashes, parentheses, or no formatting. Normalize in code.
9. For forms with more than seven fields, consider splitting into multiple steps or sections.

### Disabled-Button Decision Rule And Recovery

Disabled buttons hide the path forward and break keyboard/screen-reader navigation.

- **The Test**: If disabled, will user have to figure out how to enable it, or is the reason super obvious? If not super obvious, **do not disable**.
- **When to disable**:
  - Waiting on async response after press (prevents duplicate submissions).
  - Obvious navigation boundary (e.g., "Previous" on page 1 of wizard).
  - Minimal surveys with single required field directly in view.
- **When to keep enabled (form submit)**:
  - Multi-field forms or forms with mixed required/optional inputs.
  - Form validation errors scattered across long scroll.
- **Active Recovery Pattern**:
  - User taps enabled submit with incomplete fields.
  - Show clear error summary message.
  - Visually highlight missing/invalid fields.
  - Scroll and move focus directly to first missing field so user does not hunt.
- **Accessibility Risk**:
  - For actions that are truly unavailable, native `disabled` is appropriate; it cannot receive focus and is unavailable to keyboard tab navigation.
  - When keyboard discoverability matters, use `aria-disabled="true"` instead of `disabled`, suppress pointer and keyboard activation handlers, and provide a clear visible explanation (e.g., tooltip, descriptive text, or `aria-describedby`).

### Form Mistakes To Avoid

- Disabled button with no explanation or obvious trigger.
- Submit, wait, then scroll to find errors.
- Rejecting user input because formatting differs from preferred display format.
- Making users type data the app already knows.
- Showing all fields at once when the form feels like a wall.

## Empty States

Empty states are often first impressions. Do not leave blank areas or dead ends.

### Empty State Rules

- Explain what the area is for.
- Explain why it is empty.
- Provide a clear next action.
- Add guidance, steps, or onboarding when helpful.
- Handle every empty section, not only the page-level state.

### Empty Search Results

- "No results" is acceptable but weak.
- "No results for purple shoes" is better because it confirms the query.
- Offer an action: clear filters, try a broader search, or search the same term elsewhere if relevant.

### Goal Empty States

When emptiness is success, celebrate it:

- Inbox zero.
- No open tasks.
- All steps complete.

Use subtle delight, animation, or visual reward so the state feels achieved, not broken.

## Partial Loading And Graceful Degradation

Pages are made of sections backed by different data sources. Each section should load, fail, and recover independently.

### Rules

- Load what is available instead of waiting for everything.
- Give each section its own loading state, error state, and retry action.
- If one section fails, keep the rest of the page usable.
- Show cached content while fresh data loads when available.
- Think through mixed states: some sections loaded, some loading, some failed, some stale.

### Implementation Pattern

```text
Page
|-- Profile section: own data, loader, error, retry
|-- Feed section: own data, loader, error, retry
|-- Sidebar section: own data, loader, error, retry
`-- Charts section: own data, loader, error, retry
```

### Avoid

- Full-page loading screen until every component is ready.
- Full-page error because one API call failed.
- One failed section taking down the whole page.
- Hiding cached or stale-but-useful content during refresh.

## Success States

Users need to know their action worked. Missing success feedback creates anxiety and repeated actions.

### Success Feedback Rules

- Always close the loop after user action.
- Match feedback intensity to importance.
- Use subtle feedback for small actions like likes, toggles, or saved settings.
- Use clear confirmation for high-stakes actions like payment, booking, or submission.
- Use celebration for milestones like first project, major task completion, or achievement.
- Sometimes the changed state is confirmation, like a card moving from "To do" to "Done" and staying there.

### Avoid

- No feedback after critical action.
- Over-celebrating routine actions.
- Full-page success for actions that only need inline confirmation.
- Leaving users unsure whether they should click again.

## Button States And Accessibility

Buttons require distinct visual and tactile signals across their full lifecycle. Missing states make interfaces feel unresponsive or broken.

### The Six Button States

| State | Purpose & Appearance | Implementation Rules |
|---|---|---|
| **Default** | Indicates affordance to press | Solid fill, clear outline, or subtle drop shadow for depth. |
| **Hover** | Signals cursor interactivity | Color or border shift under cursor. **Desktop-only**: do not trigger sticky hover styles on mobile touch devices. |
| **Focus** | Critical for accessibility (keyboard / screen reader) | High-contrast outline or focus ring when tabbing. Never remove outline without accessible replacement. |
| **Pressed** | Instant acknowledgment of tap/click | Darken color, inward press animation, or mobile tactile/haptic buzz/ripple. Prevents repeated spam clicks. |
| **Loading** | Confirms background processing | Loader inside the button keeps context where user is looking without full-page disruption. |
| **Disabled** | Signals unavailable action | Faded appearance. Follow disabled-button decision rule to prevent accessibility traps. |

### Button Rules

- Always provide immediate pressed feedback. Lack of instant visual or haptic feedback causes users to hit buttons repeatedly assuming frozen UI.
- Scoped loading belongs directly inside the trigger button rather than an intrusive screen-wide takeover.
- Avoid sticky hover states on touchscreens. Touch interfaces lack hover cursors; simulate tap responses cleanly.
- Keyboard focus outlines must remain visible and legible across all themes.

## Tesler's Law (Conservation of Complexity)

Larry Tesler formulated that every system has an inherent amount of complexity that cannot be eliminated. You can only decide who bears the burden: the software builder or the user.

> "If a million users each waste a minute on a complexity that an engineer could have solved within a week's time, you are penalizing the user to make the engineer's job easier."

### Principles

- Real users are distracted, busy, and seek the path of least resistance. Do not design for ideal, patient users.
- Shift complexity into engineering and design phases:
  - Automate timestamps and cue points (e.g., Netflix "Skip Intro" detection).
  - Reduce multi-step checkout to biometric/tokenized authentication (e.g., Apple Pay with Face ID).
  - Parse and normalize messy user inputs automatically instead of forcing strict regex formats.
  - Anticipate defaults and route around manual repetitive steps.
- Prioritize developer effort over user penalty whenever complexity can be resolved in code.

## Fitts's Law, Tap Targets, And Gesture Conflicts

Fitts's Law states that the time required to rapidly move to a target area is a function of the ratio between distance to target and width of target. Larger targets and targets closer to the user's input position (thumb reach) are faster and easier to hit.

### Tap Targets And Invisible Padding

- Keep interactive icons spaced far enough apart to prevent accidental mis-taps (e.g., adjacent like, comment, share controls).
- Visual size does not equal tap size. Expand clickable area using invisible padding (hit slop) around small icons without bloating visual design.
- Expand hit zones across parent containers where applicable: make labels clickable alongside checkboxes; make whole cards clickable if directing to single destination.

### Thumb Zone And Mobile Placement

- Position high-frequency controls near bottom of screen where user thumbs rest naturally.
- Primary navigation and key action bars belong within easy one-handed reach.

### Resolving Gesture Conflicts

- Large clickable cards or list rows must discriminate between scroll gestures and tap activations.
- If swiping or scrolling triggers an accidental tap navigation, touch sensitivity and gesture arbitration require adjustment.
- Test physical devices directly: if testers miss adjacent buttons or trigger unexpected actions while scrolling, refine tap padding and gesture handling.

## Familiar Patterns And Jakob's Law

Users spend most of their time in other apps. They expect yours to follow patterns they already know. Predictable structure lets users focus on their goal instead of relearning basics.

### Rules

- Standardize placement for common components: navigation, search, cart, checkout, form patterns, account controls.
- Apply standards for the user's context, not a generic desktop default.
- Desktop web patterns differ from mobile patterns.
- Mobile layouts must account for thumb reach. Important actions often belong near the bottom.
- Locale matters. Right-to-left experiences may mirror placement, such as cart moving from top right to top left.
- Audience and market norms matter. Follow the pattern users in that context already know.
- Keep structure familiar while making visual design distinct.

### Standardize Vs Customize

| Standardize | Customize |
|---|---|
| Navigation placement | Brand color and typography |
| Cart and checkout flow | Illustration style |
| Search placement | Motion and microinteractions |
| Form layout conventions | Tone and copy |
| Error/success placement | Visual personality |

Creative placement of common controls creates friction. Creative styling of familiar structure creates personality without confusion.

## Progressive Disclosure And Discoverability

Progressive disclosure presents users with only the information and actions needed right now, revealing deeper layers as users advance through tasks.

### Core Mechanics

- Show next steps and relevant option layers as user progresses (like GPS giving turn-by-turn guidance rather than 35 turns upfront).
- Keep core surface uncluttered without deleting advanced capabilities.
- Contextual revelation:
  - On-demand AI chat / assistant tools.
  - Triggered command palettes or menus (e.g., Notion slash command exposing block options).
  - Collapsible detail views and step-by-step disclosures.

### Discoverability Caveat

- **Never hide primary or high-value features so deeply that users need a tutorial to find them.**
- Avoid deep scrolling drop-downs where key capabilities sit below the fold unnoticed (e.g., specialized video/motion creation buried under standard image/video drop-downs).
- Always ask: "Does the user need this right now, or is it cluttering their primary task?" Ensure core intent remains immediately discoverable.

## Hick's Law And Choice Complexity

Decision time increases as choices increase in number and complexity. Good UX keeps visible options manageable without removing capability.

### Rules

- Prioritize one clear primary action per screen or step.
- Reduce competing calls to action.
- Group related options.
- Curate defaults or recommendations instead of showing everything.
- Use filters, search, and categories to help users narrow options.
- Use progressive disclosure: show the next useful choice when it becomes relevant.
- Break large forms into pages, steps, or sections when field count and complexity become overwhelming (see form rule on splitting forms with more than seven fields in [Form UX](#form-ux)).

### Examples

- Google focuses the homepage on one action: search.
- Cluttered portals create unnecessary competing choices.
- Netflix has thousands of titles but shows curated rows and recommendations first.
- A long menu can show key options first, then let users search, filter, or expand.

### Avoid

- Showing every option on the first screen.
- Making secondary actions compete with the primary action.
- Hiding needed options so deeply users cannot recover them.
- Treating choice reduction as removing power. It should reduce overload while preserving access.

## Quick Decision Tables

### Loader Choice

| Situation | Use |
|---|---|
| Whole page or feed loading | Skeleton |
| File upload/download/install | Progress bar |
| Button click or small section refresh | Inline spinner (inside trigger button) |
| Like, favorite, low-risk toggle | Optimistic UI |
| Under 1 second | No loader |

### Error Placement

| Situation | Use |
|---|---|
| Invalid field | Inline field error |
| Incomplete form submission attempt | Summary message plus auto-focus/scroll to first invalid input |
| Button action failed | Inline near button |
| Non-critical background issue | Toast |
| Payment, permission, or blocker | Modal with action |

### Success Feedback

| Action | Feedback |
|---|---|
| Like/favorite/toggle | Immediate visual state change |
| Save setting | Inline saved status or subtle toast |
| Payment/booking/submission | Clear confirmation or receipt |
| Milestone/first completion | Celebration or dedicated success state |

### Button Disabled vs Enabled Rule

| Situation | Decision | Behavior |
|---|---|---|
| Active async request | Disable button | Prevents duplicate submissions and indicates processing |
| Boundary navigation (first page "Previous") | Disable button | Reason is completely obvious and requires no user guesswork |
| Single-field simple prompt | Disable button | Blocker is directly in front of the user |
| Multi-field form or scattered inputs | Keep enabled | Clicking triggers inline errors, field highlights, and focus jump to first missing field |

## Common Mistakes

| Mistake | Fix |
|---|---|
| Building only happy path | Define loading, success, error, and empty states first |
| Blank screen while loading | Show skeleton or section-level loading |
| Spinner flashes under 1 second | Show result directly |
| Infinite spinner over 10 seconds | Use progress or step indicator |
| "Something went wrong" | Explain what happened, why, and next action |
| Raw backend error shown | Map to user-safe message |
| Toast for critical error | Use inline or modal depending on blocker |
| Disabled submit with no clue | Apply disabled-button decision rule; if kept enabled, show errors and move focus to first invalid field |
| Full-page error for one failed card | Isolate section failure and keep rest usable |
| No confirmation after action | Add feedback matched to action importance |
| Missing button pressed feedback | Give instant visual or haptic feedback to prevent spam clicking |
| Sticky hover on mobile | Restrict hover styles to desktop pointer devices |
| Keyboard focus outline removed | Retain visible focus ring across all themes |
| Offloading complexity to user | Apply Tesler's Law: solve complexity in code and system design |
| Tiny or crowded tap targets | Expand hit area with invisible padding; space icons apart (Fitts's Law) |
| Scrolling triggers card clicks | Separate scroll gesture detection from tap activation |
| Desktop pattern copied to mobile | Adapt to device reach and mobile thumb zone |
| Progressive disclosure buries key tools | Keep core features visible; do not force users into tutorials |
| All options shown at once | Group, curate, filter, or disclose progressively |

## Final UX Checklist

Before shipping any UI, verify:

- [ ] UI and UX both considered: visual design plus understandable behavior.
- [ ] Loading state exists for every async page, section, and action.
- [ ] Loader type matches scope and duration (inline loaders live inside action buttons).
- [ ] No spinner appears for work under 1 second.
- [ ] Text appears for waits over 5 seconds.
- [ ] Progress or steps appear for waits over 10 seconds.
- [ ] Success state confirms every user action.
- [ ] Error messages explain what happened, why, and next action.
- [ ] No raw backend/database/stack errors are exposed.
- [ ] No silent failures.
- [ ] Errors are placed near the cause, unless they block the whole flow.
- [ ] Button states supported: default, hover (desktop only), focus, pressed, loading, and disabled.
- [ ] Instant pressed feedback provided (visual or haptic) to avoid duplicate spam clicks.
- [ ] Focus outlines remain clear and accessible for keyboard and screen-reader navigation.
- [ ] Disabled-button decision rule applied: do not disable if user must guess why; keep enabled and focus missing fields.
- [ ] Incomplete-form submissions scroll and move focus directly to the first invalid field.
- [ ] Disabled or blocked buttons preserve accessibility and discoverability: when keyboard discovery matters, use `aria-disabled="true"`, suppress activation handlers, and explain why action is blocked.
- [ ] Tesler's Law applied: engineering absorbs complexity (auto-detection, input normalization, 1-tap checkout) instead of penalizing users.
- [ ] Fitts's Law applied: tap targets are adequately sized, small icons use invisible padding, and key actions fit thumb reach.
- [ ] Gesture arbitration works: scrolling does not trigger unintended taps on clickable cards.
- [ ] Progressive disclosure balances clean UI without hiding critical features behind tutorials or deep menus.
- [ ] Empty states explain purpose, reason, and next action.
- [ ] Search empty states mention the query and offer recovery.
- [ ] Achievement empty states feel rewarding.
- [ ] Forms mark required fields and show what is missing.
- [ ] Forms validate inline and keep errors near fields.
- [ ] Forms pre-fill known data where possible.
- [ ] Inputs accept forgiving formats and normalize internally.
- [ ] Long or complex forms are grouped or split when needed.
- [ ] Page sections load and fail independently.
- [ ] Cached content is shown during refresh when available.
- [ ] Familiar patterns match device, locale, audience, and market norms.
- [ ] Common controls are standardized; brand expression stays in styling.
- [ ] Primary action is clear.
- [ ] Choices are grouped, curated, filtered, or progressively disclosed.
