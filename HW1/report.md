LLM4Rec Homework Report — Assignment A01

**Student:** Miagdeev Ruslan
**Team:** Individual
**Email:** rmmyagdeev@edu.hse.ru
**Date:** 2026-09-17
**Assignment:** A01 — Random Lunch Generator (Week 1)

---

# Random Lunch Generator: Debugging Silent Icon Failures in a Web-Based Menu Recommender

---

## Abstract

This report addresses a practical debugging problem in a simple web-based recommender: some lunch items displayed no icon at all, while others displayed identical icons for different dishes. The root causes were silent failures in the Font Awesome icon system — non-existent class names and a copy-paste duplication error — which produce no console errors and are therefore easy to miss. The fix replaced invalid classes with verified FA6 Free Solid equivalents and added Iconify as a second icon source for dishes that have no FA6 glyph. After the fix, all 12 lunch items display unique, correct icons across 50 manual button clicks. The key takeaway is that visual bugs in AI-generated code can be silent: absence of an error does not mean absence of a bug.

**Index Terms** — recommender systems, debugging, silent failure, Font Awesome, Iconify, web development

---

## §1 Introduction

**Problem statement.** A Random Lunch Generator is a simple web app that picks a random dish from a hardcoded list and displays its name and icon. Two visual defects were present: (1) some dishes had no icon at all, and (2) two different dishes (Curry and Tacos) shared the same icon.

**Motivation.** Even in a trivial recommender, visual correctness matters: the icon is the user's primary signal about the recommended item. Silent failures — where the code runs but the UI is wrong — are exactly the kind of problem that AI-generated code tends to hide, because the AI reports success while the visual output is broken.

**Concrete example.** A user clicks "Generate Lunch!". The app picks "Pasta" and shows the text "Pasta" — but no icon. No error appears in the console. The user sees a broken UI and has no idea why.

**Contributions.**
- Identified two distinct silent-failure bugs in the icon system.
- Evaluated five alternative fix strategies and chose two.
- Verified the fix with manual and DevTools-based inspection.

---

## §2 Related Work

[1] Font Awesome, "Font Awesome 6 Free Solid — Icon Library," Font Awesome, 2024. [Online]. Available: https://fontawesome.com/icons. [Accessed: 2026-09-17].

[2] Iconify, "Iconify: Unified Icon Framework," Iconify, 2024. [Online]. Available: https://icon-sets.iconify.design. [Accessed: 2026-09-17].

[3] Mozilla Developer Network, "Developer console," MDN Web Docs, 2024. [Online]. Available: https://developer.mozilla.org/en-US/docs/Learn/Common_questions/Tools_and_setup/What_are_browser_developer_tools. [Accessed: 2026-09-17].

**Alternatives considered.**
- **Emoji icons** — simple and offline, but visual style is inconsistent with vector icons. Rejected for aesthetic reasons.
- **Inline SVG** — self-contained, but requires maintaining SVG markup. Rejected as overkill for 12 items.
- **Hosted PNG/SVG files** — flexible, but adds file management overhead. Rejected for this small project.

---

## §3 Method

**Approach.** The app is a single-page HTML file with embedded CSS and JavaScript. The lunch list is a hardcoded array of objects (`{ name, icon }`). The "Generate Lunch!" button triggers a random selection via `Math.random()`, and the chosen icon and name are injected into the DOM.

**Pipeline (step-by-step).**
1. User clicks "Generate Lunch!".
2. `generateRandomLunch()` picks a random index from `lunchMenu`.
3. The display area briefly shows a loading spinner.
4. After 500 ms, the chosen icon and name are rendered.
5. A fade-in animation plays on the result.

**Tools & libraries.**
- HTML5 / CSS3 / vanilla JavaScript.
- Font Awesome 6.4.0 (CDN: cdnjs.cloudflare.com).
- Iconify 3.1.0 (CDN: code.iconify.design) — added as a second icon source for dishes without FA6 glyphs.
- Browser: Chrome with DevTools (Console + Network tabs).

**Key design decisions.**
- **Why two icon libraries:** Tacos and Curry have no FA6 Free Solid glyphs (verified against fontawesome.com/icons and Iconify sets). Using Iconify only for those two dishes keeps the rest of the code unchanged and avoids a full migration.
- **Why replace invalid classes instead of using emoji:** FA6 provides consistent vector icons that scale cleanly; emoji would mix rendering styles across platforms.
- **Why verify with the Network tab:** the silent failure produced no console error, so the only reliable signal was the request log.

**Configuration.**
- Browser: Chrome (latest).
- Test environment: local `index.html` opened via `file://`.
- No build step required.

---

## §4 Experiments

**Setup.** The app was opened in Chrome. DevTools was opened with Console and Network tabs visible. The "Generate Lunch!" button was clicked 50 times to cover all 12 dishes at least once.

**Results.**

| Bug | Symptom | Root cause | Fix | Verified |
|---|---|---|---|---|
| #1 | Pasta, Ramen, Soup had no icon | Non-existent FA6 classes (fa-pasta, fa-bowl-hot, fa-bowl) silently ignored by FA | Replace with valid FA6 classes | 50 clicks, all 12 dishes checked |
| #2 | Curry and Tacos showed the same icon | Both dishes were assigned fa-utensil-spoon (copy-paste) | Curry → mdi:bowl-mix; Tacos → game-icons:tacos (Iconify) | Visually confirmed distinct icons |

**Comparison vs. baseline (before fix).**

| Before fix | After fix |
|---|---|
| Pasta, Ramen, Soup — no icon | All have icons |
| Tacos and Curry — same icon | Different, dish-specific icons |
| No console errors | No console errors (unchanged) |

**Verification.**
- Clicked the button 50 times until all 12 dishes appeared.
- For each dish, visually confirmed the icon is present and correct.
- Cross-checked every `fas` class against https://fontawesome.com/icons.
- Cross-checked `game-icons:tacos` and `mdi:bowl-mix` against https://icon-sets.iconify.design.
- Inspected Network tab: all CDN resources (all.min.css, iconify.min.js) returned HTTP 200.

**Figures.**

![Figure 1: Pasta without icon (before fix)](HW1/empty_pasta.png)

![Figure 2: Pasta with icon (after fix)](HW1/new_pasta.png)

![Figure 3: Ramen without icon (before fix)](HW1/empty_ramen.png)

![Figure 4: Ramen with icon (after fix)](HW1/new_ramen.png)

![Figure 5: Curry and Tacos showing the same icon (before fix)](HW1/empty_curry.png)

![Figure 6: Curry with a distinct icon (after fix)](HW1/new_curry.png)

![Figure 7: Curry and Tacos showing the same icon (before fix)](HW1/not_correct_picture_tacos.png)

![Figure 8: Tacos with a distinct icon (after fix)](HW1/new_tacos.png)

---

## §5 Discussion

**Failure case.** I clicked the button and saw that Pasta had no icon — only the word "Pasta". Same for Ramen and Soup. Then I noticed Curry and Tacos both showed the same spoon icon. No error in the console.

**Root cause.** Two distinct root causes were found. First, the lunchMenu array used Font Awesome class names that do not exist in FA 6.4.0 (fa-pasta, fa-bowl-hot, fa-bowl). Font Awesome's CSS silently ignores unknown class names — no error, no warning, no fallback. The UI simply renders empty space. Second, Curry and Tacos were both assigned the same existing class (fa-utensil-spoon) — a copy-paste mistake — so they rendered identically. Both are silent failures: the code looks correct, but the visual output is wrong.

**Fix + verification.** Two changes were made. First, invalid classes were replaced with verified FA6 Free Solid equivalents (fa-plate-wheat for Pasta, fa-bowl-rice for Ramen, fa-bowl-food for Soup). Second, for Curry and Tacos — neither of which has a suitable FA6 glyph — I added Iconify as a second icon source using the span markup `<span class="iconify" data-icon="set:icon">` (mdi:bowl-mix for Curry, game-icons:tacos for Tacos). Verification was done in two steps: (a) manually clicking the button 50 times until all 12 dishes appeared, and (b) cross-checking each class name against the official Font Awesome and Iconify catalogs. After the fix, all 12 dishes display unique, correct icons.

**What worked.**
- Splitting "no icon" and "wrong icon" into two separate bugs made the fix straightforward and the verification concrete.
- DevTools → Network was the only reliable diagnostic tool, because the bug produced no console error.
- Using two icon sources (FA + Iconify) was the simplest way to cover dishes without FA6 glyphs.

**What surprised me.**
- Font Awesome does not warn about unknown class names. I expected a console error or at least a fallback box; there is none. This is what makes the bug "silent".
- Adding a second library was simpler than migrating the whole icon set. Two lines of setup and one span per dish solved the problem.
- The same visual symptom (broken icons) had two completely different root causes — one missing class, one duplicated class.

**Next improvement.** Add a runtime check that verifies each icon resolves to a non-empty glyph after rendering, and logs a warning if not. This would catch silent failures automatically instead of relying on manual clicks. A second improvement would be to pre-render icons inline as SVG at build time, removing the CDN dependency entirely and making the app fully offline-capable.

---

## §6 AI Usage Disclosure

**AI tools used.**
- OpenCode CLI with Controlled-AI harness (build + code + review agents).
- opencode/mimo-v2.5-free

**How AI was used.**
- Code generation: initial index.html scaffold and fix suggestions.
- Debugging: proposed the "silent failure" hypothesis for missing icons.
- Documentation: draft structure for this report.

**What I personally verified.**
- Clicked the button 50 times and inspected each of the dishes visually.
- Cross-checked every Font Awesome class against the official catalog.
- Cross-checked Iconify icon names against the Iconify catalog.
- Inspected the Network tab to confirm CDN resources loaded (HTTP 200).
- Reproduced the bug before applying the fix (missing icons for Pasta, Ramen, Soup; duplicate icon for Curry and Tacos).

**What I trusted without verification.**
- The exact FA6 version compatibility of every single icon beyond the ones used in this app. I only verified the 12 dishes shown in the UI.
- The behavior of the app on mobile browsers. I tested only on desktop Chrome.

**Session log.** Attached as `session.json` — contains every prompt sent to the agent, every file modification, and timestamps.

---

## References

[1] Font Awesome, "Font Awesome 6 Free Solid — Icon Library," Font Awesome, 2024. [Online]. Available: https://fontawesome.com/icons. [Accessed: 2026-09-17].

[2] Iconify, "Iconify: Unified Icon Framework," Iconify, 2024. [Online]. Available: https://icon-sets.iconify.design. [Accessed: 2026-09-17].

[3] Mozilla Developer Network, "Developer console," MDN Web Docs, 2024. [Online]. Available: https://developer.mozilla.org/en-US/docs/Learn/Common_questions/Tools_and_setup/What_are_browser_developer_tools. [Accessed: 2026-09-17].