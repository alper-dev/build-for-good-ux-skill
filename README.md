<p align="center">
  <img src="assets/banner.png" alt="Build For Good UX by Katherine Gilligan" width="500">
</p>

<p align="center">An agent skill for building UIs that handle loading, success, error, empty, partial, familiar-pattern, and choice-heavy states with good UX.</p>

<p align="center">
  <img src="https://img.shields.io/badge/agent--skill-SKILL.md-00ADD8?style=flat" alt="Agent Skill">
  <img src="https://img.shields.io/badge/license-Apache%202.0-green?style=flat" alt="License">
  <img src="https://img.shields.io/github/stars/alper-dev/build-for-good-ux-skill?style=flat&color=yellow" alt="Stars">
</p>

---

Based on the **Build For Good UX** video series by [Katherine Gilligan](https://instagram.com/synsation_) ([@synsation_](https://instagram.com/synsation_)). All principles are from her publicly available, free Instagram posts.

## What's Covered

| | Topic | Details |
|---|---|---|
| 🎨 | **UI vs UX** | Beautiful interface plus understandable behavior, recovery, and trust |
| ⬜ | **Loading States** | Skeleton screens, progress bars, spinners, optimistic UI |
| ⏳ | **Spinner Timing** | When to show what based on duration (<1s, 1-10s, >10s) |
| ⚠️ | **Error States** | Message quality, placement (inline, toast, modal) |
| ✅ | **Success States** | Action feedback, celebrations, subtle confirmations |
| 🌵 | **Empty States** | First impressions, search results, goal/achievement states |
| 📄 | **Form UX** | Inline validation, pre-filling, character counts, forgiving formats |
| 🧩 | **Graceful Degradation** | Independent sections, cached content, partial loading |
| 🧭 | **Jakob's Law** | Familiar patterns by device, locale, audience, and market |
| 🫙 | **Hick's Law** | Reduce decision complexity with grouping, filtering, curation, progressive disclosure |
| 👁️ | **Progressive Disclosure** | Show steps contextually without burying critical features behind tutorials |
| ⚙️ | **Tesler's Law** | Absorb complexity in code and system design instead of penalizing users |
| 🔘 | **Button States & Accessibility** | Default, hover, focus, pressed, loading, and disabled states |
| 🚫 | **Disabled-Button Rule** | Keep submit enabled with field jump unless blocker is completely obvious |
| 🎯 | **Fitts's Law & Touch** | Hit target padding, thumb reach, spacing, scroll-vs-tap conflict prevention |

## Installation

```bash
# npm
npx skills add alper-dev/build-for-good-ux-skill

# yarn
yarn skills add alper-dev/build-for-good-ux-skill

# bun
bunx skills add alper-dev/build-for-good-ux-skill
```

If you find this useful, consider giving it a star. It helps others discover it.

## License

Apache 2.0
