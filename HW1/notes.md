# A01 — Notes

## Bug #1: Some dishes had no icon at all

**Symptom:** For dishes Pasta, Ramen, Soup the icon did not appear.
Only the dish name was visible — empty space where the icon should be.

**Hypothesis:** The lunchMenu array used Font Awesome classes that
do not exist in version 6.4.0 (fa-pasta, fa-bowl-hot, fa-bowl).
Font Awesome silently ignores unknown classes — no error in the
console, but no icon either. This is a silent failure: the code
looks correct, the browser reports no problem, but the visual
result is wrong.

**5 fix options considered:**
1. Rename classes to valid FA6 equivalents (simplest).
2. Use emoji instead of Font Awesome (offline-friendly, but different style).
3. Use inline SVG (self-contained, more code to maintain).
4. Use a different icon library (Bootstrap Icons, Lucide, Material Icons).
5. Host PNG/SVG files in the repo (most flexible, adds file management).

**Chosen fix:** option 1 — replace invalid classes with valid FA6 ones.

| Dish   | Before (invalid)  | After (valid)         |
|--------|-------------------|-----------------------|
| Ramen  | fa-bowl-hot       | fas fa-bowl-rice      |
| Pasta  | fa-pasta          | fas fa-plate-wheat    |
| Soup   | fa-bowl           | fas fa-bowl-food      |

**Before:** Pasta, Ramen, Soup → empty space instead of an icon.
**After:** all three dishes → icons are displayed.

**Verification:**
- Opened DevTools → Console. No errors (FA stays silent — that's the trap).
- Network tab: all.min.css loads from CDN (HTTP 200).
- Clicked the button 50 times until I saw all 12 dishes:
  Pizza, Sushi, Burger, Salad, Tacos, Ramen, Sandwich,
  Pasta, Curry, Steak, Soup, BBQ.
- For each dish, visually confirmed the icon is displayed. ✅
- Cross-checked every `fas` class against https://fontawesome.com/icons.
- Screenshots attached.

---

## Bug #2: Curry and Tacos showed the same icon

**Symptom:** Curry and Tacos displayed the same icon
(fa-utensil-spoon). The icon was present, but it did not match
the dishes — two different meals looked visually identical.

**Hypothesis:** In the lunchMenu array, both dishes were assigned
the same class (fa-utensil-spoon). A copy-paste mistake — the
author duplicated a line and forgot to change the icon.

Additionally, Tacos had no suitable FA6 Free Solid glyph even if
we wanted a distinct one, so a second icon source was needed.

**Chosen fix:** assign distinct, dish-specific icons using Iconify
for both dishes (since neither has a fitting FA6 glyph).
- Curry → mdi:bowl-mix (Iconify)
- Tacos → game-icons:tacos (Iconify)

**Before:** Curry and Tacos → same icon (fa-utensil-spoon).
**After:** Curry → mdi:bowl-mix, Tacos → game-icons:tacos
(two distinct icons that match the dishes).

**Verification:**
- Clicked the button until Curry appeared — confirmed its icon.
- Clicked the button until Tacos appeared — confirmed its icon.
- Visually confirmed the two icons are different and each matches
  its dish.
- Cross-checked `game-icons:tacos` and `mdi:bowl-mix` against
  https://icon-sets.iconify.design.
- Screenshots attached.

---

## Agent session
- session.json: [path]
- Prompts used: [brief description of what I asked the agent]