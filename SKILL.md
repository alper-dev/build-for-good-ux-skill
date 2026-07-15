---
name: build-for-good-ux
description: Use when building, reviewing, or improving user-facing UI, frontend components, pages, forms, flows, loading states, error handling, empty states, success feedback, graceful degradation, familiar layouts, or choice-heavy interfaces.
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
3. Pick the loader based on scope and duration.
4. Place errors next to the thing that caused them unless the issue blocks the whole flow.
5. Ensure every action gives visible feedback.
6. Make each page section resilient when other sections load slowly or fail.
7. Use familiar patterns for the user's device, locale, and audience.
8. Reduce choice overload with grouping, curation, filtering, or progressive disclosure.
9. Verify the final checklist at the bottom of this file.

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

1. Disable submit until required fields are valid, but explain what is missing.
2. Mark required fields clearly so users are never guessing why submit is disabled.
3. Validate inline when users leave a field, not only after submit.
4. Keep validation messages near the field, not at the top of the page.
5. Show character counts for limited fields.
6. Pre-fill known values, such as logged-in user's email.
7. Show password requirements while typing and check them off live.
8. Accept forgiving formats, such as phone numbers with spaces, dashes, parentheses, or no formatting. Normalize in code.
9. For forms with more than seven fields, consider splitting into multiple steps or sections.

### Form Mistakes To Avoid

- Disabled button with no explanation.
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

## Hick's Law And Choice Complexity

Decision time increases as choices increase in number and complexity. Good UX keeps visible options manageable without removing capability.

### Rules

- Prioritize one clear primary action per screen or step.
- Reduce competing calls to action.
- Group related options.
- Curate defaults or recommendations instead of showing everything.
- Use filters, search, and categories to help users narrow options.
- Use progressive disclosure: show the next useful choice when it becomes relevant.
- Break large forms into pages, steps, or sections when field count and complexity become overwhelming.
- Consider multi-step forms when there are more than seven fields.

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
| Button click or small section refresh | Inline spinner |
| Like, favorite, low-risk toggle | Optimistic UI |
| Under 1 second | No loader |

### Error Placement

| Situation | Use |
|---|---|
| Invalid field | Inline field error |
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
| Disabled submit with no clue | Mark required fields and explain missing input |
| Full-page error for one failed card | Isolate section failure and keep rest usable |
| No confirmation after action | Add feedback matched to action importance |
| Desktop pattern copied to mobile | Adapt to device reach and mobile norms |
| All options shown at once | Group, curate, filter, or disclose progressively |

## Final UX Checklist

Before shipping any UI, verify:

- [ ] UI and UX both considered: visual design plus understandable behavior.
- [ ] Loading state exists for every async page, section, and action.
- [ ] Loader type matches scope and duration.
- [ ] No spinner appears for work under 1 second.
- [ ] Text appears for waits over 5 seconds.
- [ ] Progress or steps appear for waits over 10 seconds.
- [ ] Success state confirms every user action.
- [ ] Error messages explain what happened, why, and next action.
- [ ] No raw backend/database/stack errors are exposed.
- [ ] No silent failures.
- [ ] Errors are placed near the cause, unless they block the whole flow.
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
