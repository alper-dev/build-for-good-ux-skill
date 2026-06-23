---
name: build-for-good-ux
description: Use this skill when building any UI, frontend component, page, form, or user-facing feature. Ensures every screen handles all states (loading, success, error, empty, partial) with proper UX patterns.
when_to_use: Triggers on requests involving UI development, component creation, page design, form building, error handling, loading states, skeleton screens, spinners, toast messages, empty states, success confirmations, partial loading, graceful degradation, or any user-facing interface work. Also triggers when the user asks about UX best practices, how to handle loading/error/empty states, or what makes a good user experience.
---

# Build For Good UX

> Based on the "Build For Good UX" video series by **Katherine Gilligan** ([@synsation_](https://instagram.com/synsation_) on Instagram). All UX principles and patterns in this skill are derived from her publicly available, free Instagram posts, not from any paid course material.

## Core Principle

Every screen in your app has **four states** you must build: **Loading, Success, Error, Empty**. If you skip any, users will notice and assume something is broken. AI tools naturally generate the "happy path" (what the screen looks like when everything works). You must explicitly handle the other states.

Good UX builds trust. Bad UX (unpredictable results, too many steps, friction) causes users to leave, and they will blame your product, not themselves.

---

## 1. Loading States

### Choosing the Right Loader

| Pattern | When to Use | Example |
|---------|-------------|---------|
| **Skeleton screen** | Entire page or large section of content loading | Feeds, dashboards, profile pages (Instagram, LinkedIn, YouTube style) |
| **Progress bar** | Duration is known or predictable | File uploads, downloads, installations |
| **Inline spinner** | Small, contained action in a specific area | Button clicked, one section refreshing |
| **Optimistic UI** | Action is very likely to succeed | Likes, favorites, toggles, assume success instantly, roll back on failure |

### Spinner Timing Rules

- **Under 1 second**: Show no loader at all. A spinner that flashes makes the app feel slower.
- **1-2 seconds**: Plain spinner, no text needed.
- **2-5 seconds**: Spinner is fine, but no text starts feeling broken past 5 seconds.
- **5-10 seconds**: Add static text ("Loading...", "Saving...") or better, changing text ("Connecting to your account..." then "Almost there...").
- **Past 10 seconds**: Looped animations stop working and increase frustration. Switch to a progress bar or step-by-step indicator.
- **If it fails**: Show the error immediately. Never make users wait 20 seconds with a spinner then show an error.

### Skeleton Screen Rules

- Show the page structure/layout first as gray placeholder outlines.
- The brain processes the layout before data arrives, making the wait feel shorter.
- Use inline spinners inside buttons or small areas where skeletons do not make sense.

### Optimistic UI Rules

- Update the UI immediately when the user takes an action.
- Do not wait for server confirmation.
- If the action fails later, roll back the change gracefully.
- Example: Instagram heart turns red instantly on tap.

---

## 2. Error States

### Error Message Rules

A good error message does three things:
1. **Tells them what happened**
2. **Tells them why it happened**
3. **Gives them a clear next action**

**Bad examples:**
- Dumping raw database/backend error on screen (security risk + unreadable)
- "Something went wrong" (too vague, user does not know if action succeeded)
- Silent failure: button does nothing, no message at all (worst case)

**Good example:**
> "Your payment didn't go through. Your card was declined. Please check your card details or try a different payment method."

### Error Placement

| Type | When to Use | Rules |
|------|-------------|-------|
| **Inline** (next to the element) | Form validation, button failures, field-specific errors | Closest to the problem. Use most often. Red border on field + message next to it. |
| **Toast** (auto-dismissing popup) | Non-critical, recoverable messages | "Couldn't connect, retrying..." Never for important errors, user might look away and miss it. |
| **Modal** (center screen, blocks interaction) | Critical errors that require user action | Payment failure, permission error. Must provide a way forward (button to update payment, request access). Use sparingly. |

### General Error Rules

- The closer the error is to the element that caused it, the better.
- Never expose backend/database details to the user.
- Always give the user a way to recover or retry.
- Never silently fail, the user must always know the outcome of their action.

---

## 3. Empty States

Empty states are often the **first thing a new user sees**. Make a good impression.

### Rules for Empty States

1. **Never leave it blank.** A blank dashboard with no actions is a dead end.
2. **Tell the user what the section is for** and how to start using it.
3. **Provide a clear call-to-action.** "Create your first project" with a button.
4. **Add guidance.** Step-by-step or gamified instructions to get them started.
5. **For every section** without content, explain its purpose and how to populate it.

### Empty Search Results

- "No results" is fine, but "No results for **purple shoes**" with a link to retry that search is better.
- Keep the user moving forward.

### Goal/Success Empty States

- When the empty state is an achievement (inbox zero, cleared tasks), celebrate it.
- Add an animation or a visually rewarding background.
- Make it something the user looks forward to seeing.

### What a Good Empty State Does

- Tells the user **why** it is empty.
- Shows them **what to do next**.
- Does **not feel broken**.

---

## 4. Success States

Success states are taken for granted when present, but very noticeable when missing. When a user completes an action, they need to know it worked.

### Rules

- **Always give feedback.** Button clicked, payment submitted, photo liked, the user must know it succeeded.
- **Match the response to the action.** A like is instant and subtle. A payment is important and needs clear confirmation.
- **Do not overdo it.** Not every success needs confetti or a full-page celebration. Use big celebrations for milestones (first project, completed task), not for every button click.
- **Sometimes the action itself is the confirmation.** Moving a card from "to do" to "done" on a board, it stays in "done". The state change is the feedback. No extra message needed.
- **Never leave the user guessing.** "I clicked confirm and nothing happened" is one of the worst feelings. Did it work? Should I click again? Always close the loop.

### What NOT to Do

- Do not show nothing after a critical action (payment, submission, booking).
- Do not over-celebrate trivial actions (toggling a setting, liking a post).
- Do not use a full-page success screen for something that could be a subtle inline confirmation.

---

## 5. Forms

Nobody likes filling out forms. Reduce friction with these rules:

1. **Disable submit until all required fields are valid.** Keep the button grayed out. But make it obvious what is missing, a grayed-out button with no explanation is worse.
2. **Validate inline.** The moment a user leaves a field (on blur), validate it. Never make them fill out the whole form, submit, then scroll up to find the error.
3. **Show character count.** If a field has a limit, show remaining characters as they type.
4. **Pre-fill what you can.** If the user is logged in, do not make them type their email again.
5. **Show password requirements as they type.** Check off each requirement in real-time (capital letter, number, length).
6. **Be forgiving with formatting.** Phone numbers with dashes, parentheses, spaces, or nothing at all, handle all formats and normalize on the backend.

---

## 6. Partial Loading / Graceful Degradation

Different parts of a page load at different speeds. They come from different servers, different APIs.

### Rules

- **Load what is available.** Do not wait for everything to be ready. Show the page as sections arrive.
- **Each section is independent.** Every section manages its own data, loading state, and error state.
- **If one section fails, the rest still work.** The page does not go down because one component broke.
- **Show cached content while fresh data loads.** Display a cached version from earlier, then swap in fresh data when ready. The user never notices the trick.

### Implementation Pattern

```
Page
├── Profile Section    → own fetch, own loader, own error
├── Feed Section       → own fetch, own loader, own error
├── Sidebar Section    → own fetch, own loader, own error
└── Charts Section     → own fetch, own loader, own error
```

Each section:
- Shows its own skeleton/spinner while loading.
- Displays its own error + retry button if it fails.
- The rest of the page remains fully usable.

### What NOT to Do

- Do not show a full-page loading screen until every component is ready.
- Do not show a full-page error if one thing breaks.
- Do not let one failed API call take down the entire page.

---

## 8. Jakob's Law (Familiarity)

Users spend most of their time on other websites and apps. They already expect your site to work the same as others. Predictable structure lets users focus on what actually matters, not on figuring out where things are.

### Rules

- **Standardize placement of common components.** Shopping cart goes top right. Navigation goes top or left. Search is usually top center or top right. Do not reinvent these patterns.
- **Being "innovative" with standard placements creates unnecessary friction.** Moving the cart to the bottom left might feel creative, but the user now has to think about where it is. That microsecond of confusion is avoidable.
- **Predictable does not mean boring.** The layout and structure can be familiar while the visual design is unique. Let the user focus on your product, not on finding the cart.
- **When in doubt, follow the pattern.** If every major app does something a certain way, there is a reason. Users have built habits around those patterns.

### What to Standardize vs. Where to Be Creative

| Standardize (zero friction) | Creative freedom |
|---|---|
| Navigation placement | Visual design, colors, typography |
| Cart/checkout flow | Content, copy, imagery |
| Form layouts | Animations, transitions |
| Search placement | Feature-specific interactions |

---

## 9. General UX Rules for AI-Generated Code

When building with AI tools or generating code, explicitly request:

1. **All four states** for every screen: loading, success, error, empty.
2. **Error messages** that are human-readable, explain what/why, and give an action.
3. **No silent failures**, every user action must have a visible outcome.
4. **Loading indicators** appropriate to the duration and context.
5. **Form validation** inline, with clear feedback on what is missing.
6. **Graceful degradation**, independent sections that do not take each other down.
7. **Empty states** with guidance, not blank screens.
8. **Pre-filling** user data where possible.
9. **Forgiving input formats**, normalize on the backend, not the user.
10. **Accessible error placement**, errors close to the element, not at the top of the page.

---

## Quick Checklist

Before shipping any screen, verify:

- [ ] Loading state: skeleton, spinner, or progress bar as appropriate
- [ ] Success state: clear confirmation of the action
- [ ] Error state: human-readable message with what/why/action
- [ ] Empty state: explanation + call-to-action
- [ ] No silent failures
- [ ] Forms: inline validation, disabled submit until valid, pre-fill where possible
- [ ] Partial loading: sections load independently, failures are isolated
- [ ] Spinner timing: no spinner for <1s, text after 5s, progress bar after 10s
- [ ] Error placement: inline for forms/toasts for minor/modal for critical
- [ ] Jakob's Law: standard placement for common components, no reinventing patterns
