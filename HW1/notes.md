Hypothesis: Code tries to get classes that don't exist. If smth does not exist, we don't get error, instead of that we silently skip the picture.

So we can try 1 of 5 options how to fix it:
Option 1: Rename classes (simplest)
Just swap invalid names for valid FA6 equivalents. One-line change per item.

Option 2: Use emoji instead of Font Awesome
Replace the entire icon system with Unicode emoji:
{ name: "Tacos", icon: "🌮" }
No CDN dependency, works offline, zero setup. But you lose the consistent vector-icon style.

Option 3: Use inline SVG
Embed small SVG drawings directly in the HTML. Fully self-contained, no external dependency, pixel-perfect control. But more code to maintain.

Option 4: Use a different icon library
Swap Font Awesome for Bootstrap Icons, Lucide, or Material Icons — each has its own CDN link and class naming scheme. Same concept, different provider.

Option 5: Use images
Host small PNG/SVG files in the repo. Most flexible visually, but adds file management overhead.