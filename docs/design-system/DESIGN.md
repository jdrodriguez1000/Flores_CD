# Design System Document: Clinical Precision & Architectural Depth

## 1. Overview & Creative North Star
The Creative North Star for this design system is **"The Clinical Sanctuary."** 

In a laboratory environment, clarity is synonymous with safety, and precision is the highest form of professional courtesy. To move beyond a generic "medical portal," this system rejects the cluttered, line-heavy interfaces of the past. Instead, it adopts a high-end editorial approach: using expansive white space, intentional asymmetry, and "Surface Nesting" to guide the eye. We are building an environment that feels as sterile and advanced as a modern research facility, yet as intuitive as a bespoke editorial piece.

The "template" look is broken by treating the screen not as a flat canvas, but as an architectural space where data "floats" on layered planes of glass and light.

---

## 2. Colors: Tonal Architecture
The palette is rooted in "Scientific Blue" and "Pristine White," but it gains depth through the strategic use of Material Design tonal containers.

### The "No-Line" Rule
**Explicit Instruction:** You are prohibited from using 1px solid borders to define sections or containers. Boundaries must be established through background shifts.
*   **Base Layer:** Use `surface` (#f8f9ff) for the main background.
*   **Sectioning:** Use `surface-container-low` (#f1f3fc) to define a sidebar or a secondary content area.
*   **Primary Focus:** Use `surface-container-lowest` (#ffffff) for the main work area or data cards.

### Surface Hierarchy & Nesting
Treat the UI as a series of physical layers. An inner container (like a search bar within a header) should use a higher tier of surface (e.g., `surface-container-highest`) to suggest it is "closer" to the user.

### The "Glass & Gradient" Rule
To elevate the "Scientific" feel:
*   **Floating Navigation:** Apply a `backdrop-blur` of 12px to `surface` at 80% opacity for top bars.
*   **Signature Gradients:** For high-impact areas (Hero headers or Main Action buttons), use a subtle linear gradient from `primary` (#00478d) to `primary-container` (#005eb8). This adds "soul" and a sense of fluid energy to the clinical blue.

---

## 3. Typography: The Modernist Lens
We use a dual-font strategy to balance technical authority with human readability.

*   **Display & Headlines (Manrope):** This is our "Scientific Modernist." Its geometric structure feels engineered. Use `display-lg` for key metrics or laboratory names to establish an authoritative, premium tone.
*   **Body & Labels (Inter):** This is our "Invisible Workhorse." Designed for maximum legibility at small sizes, use Inter for all form labels (`label-md`), data tables (`body-md`), and status updates.

**Editorial Tip:** Use `headline-sm` in `primary` color for section headers, but set them with wide letter-spacing (0.05em) to give the lab interface a sophisticated, breathable feel.

---

## 4. Elevation & Depth
In this system, depth is communicated through **Tonal Layering**, not structural shadows.

*   **The Layering Principle:** Instead of a shadow, place a `surface-container-lowest` card on a `surface-container-low` background. The subtle shift from #ffffff to #f1f3fc creates a "soft lift" that feels architectural rather than "tacked on."
*   **Ambient Shadows:** If an element must float (e.g., a critical decision modal), use a shadow with a 32px blur, 4% opacity, using the `on-surface` (#181c22) color. It should feel like a soft glow, not a dark edge.
*   **The "Ghost Border" Fallback:** If accessibility requires a container edge, use the `outline-variant` token at **20% opacity**. Never use 100% opaque borders.

---

## 5. Components

### Form Inputs
*   **Styling:** Inputs should not have a bottom line or a 4-sided border. Use `surface-container-highest` as a solid background fill with a `sm` (0.125rem) roundedness.
*   **Focus State:** Transition the background to `primary-fixed` and add a subtle 2px "Ghost Border" of `primary`.
*   **Labels:** Always use `label-md` in `on-surface-variant`.

### Decision Alerts (Continue/Change)
*   **Action Containers:** When a user must decide, use a "Surface Nesting" block. 
*   **The "Continue" Action:** Use a `primary` button. High contrast, solid fill, `md` (0.375rem) roundedness.
*   **The "Change/Modify" Action:** Use a `secondary-container` button with `on-secondary-container` text. This provides a clear visual "step down" from the primary path without losing professional polish.
*   **Status Alerts:** Use `tertiary-container` for "Change Required" alerts (the warm amber-orange tone provides a "scientific caution" look) and `primary-container` for "System Ready" states.

### Cards & Lists
*   **Forbidden:** 1px divider lines.
*   **Alternative:** Use 24px of vertical white space to separate list items. For complex data, use alternating row backgrounds (Zebra striping) using `surface` and `surface-container-low`.

### Specialized Lab Components
*   **Status Micro-chips:** Use `full` roundedness (capsule shape). A "Completed" test uses `primary-fixed` background; a "Pending" test uses `surface-variant`.
*   **Data Density:** Use `body-sm` for technical metadata to allow for high information density without cluttering the visual field.

---

## 6. Do's and Don'ts

### Do:
*   **Embrace Asymmetry:** Align the main lab results to the left, but leave a wide, empty "gutter" on the right for a cleaner, editorial look.
*   **Use Surface Tones:** Always ask "can I separate these two areas using a color shift instead of a line?"
*   **Prioritize Hierarchy:** Use `display-md` for the most important number on the screen (e.g., a PH level or a count).

### Don'ts:
*   **No Pure Black:** Never use #000000. Use `on-surface` (#181c22) for text to maintain a premium, softer contrast.
*   **No Default Shadows:** Avoid the "floating card" look common in cheap templates. Stick to tonal layering.
*   **No Clutter:** If a piece of information isn't vital to the current "Decision Step" (Continue/Change), hide it in a "details" hover state.