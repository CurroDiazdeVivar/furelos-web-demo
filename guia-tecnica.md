=============== GUIA: navbar ===============

--- Guide for navbar ---
# Responsive Site Navigation

Build a site navigation bar that uses one semantic navigation tree at every size, presents a native popover when the links do not fit, becomes an inline navigation when they do, and identifies the current page.

This guide describes site-navigation integration. It is navigation, not an application menu: links stay in the `Tab` order, with no `focusgroup`, `role="menubar"`, `role="menu"`, `role="menuitem"`, `aria-haspopup="menu"`, or arrow-key handling. For application command menus, see `menu` (via `npx -y modern-web-guidance@latest retrieve "menu"`); for menubars, see `app-menus` (via `npx -y modern-web-guidance@latest retrieve "app-menus"`). For the menu and chevron icons, see `icons` (via `npx -y modern-web-guidance@latest retrieve "icons"`).

For component-driven layouts, fluid sizing, and typographic line-wrapping, see `size-aware-styling` (via `npx -y modern-web-guidance@latest retrieve "size-aware-styling"`), `fluid-scaling` (via `npx -y modern-web-guidance@latest retrieve "fluid-scaling"`), and `improve-text-layout-and-legibility` (via `npx -y modern-web-guidance@latest retrieve "improve-text-layout-and-legibility"`).


## Core Markup

Use one `<nav>` landmark for narrow and wide layouts. Wrap it in a semantic `<header>` whose `is-wide` class selects the inline layout. The collapsed layout is the default, so it is also what you get without script.

```html
<header class="site-header">
  <div class="header-inner">
    <a class="site-logo" href="index.html">Acme</a>

    <nav class="site-nav" aria-label="Primary">
      <button
        class="menu-button"
        type="button"
        popovertarget="site-menu"
        popovertargetaction="toggle"
        aria-controls="site-menu"
      >
        <!-- Decorative menu icon: insert as described in the icons guide -->
        <span>Menu</span>
      </button>

      <ul id="site-menu" class="site-menu" popover="auto">
        <li><a href="index.html" aria-current="page">Home</a></li>
        <li><a href="about.html">About</a></li>
        <li class="nav-dropdown">
          <button
            class="dropdown-trigger"
            type="button"
            aria-expanded="false"
            aria-controls="services-menu"
            popovertarget="services-menu"
          >
            Services
          </button>
          <ul id="services-menu" class="dropdown-list">
            <li><a href="design.html">Design</a></li>
            <li><a href="development.html">Development</a></li>
          </ul>
        </li>
      </ul>
    </nav>
  </div>
</header>
```

The button declaratively controls the menu with `popovertarget` and `aria-controls`. The `nav` remains the navigation landmark while the menu is closed. The trigger icon is decorative and the visible "Menu" text is the accessible name, so hide the icon from assistive technologies. For how to insert and hide icons, see `icons` (via `npx -y modern-web-guidance@latest retrieve "icons"`). For the general popover control contract, see `declarative-dialog-popover-control` (via `npx -y modern-web-guidance@latest retrieve "declarative-dialog-popover-control"`). Unlike the menu guide, use `popovertarget`: one toggle needs no invoker commands. As in the menu guide, place each popover (the menu list, and the submenu list) immediately after its trigger so browsers add no implicit `aria-details`.


## Narrow Layout: Popover Navigation

When collapsed, keep `popover="auto"` on the navigation list. Anchor the fixed popover to the menu button and constrain its height so a long navigation can scroll without exceeding the viewport.

There is no hard-coded breakpoint. Script collapses the navigation only when the inline row (brand, actions and every link) does not fit, so adding a link, a translation or larger text moves the point automatically. It adds `is-wide` to the header when the row fits; without script the header keeps the collapsed layout at every width.

```css
/* Keep the row's natural width measurable */
.header-inner > * {
  flex-shrink: 0;
  white-space: nowrap;
}

.site-header {
  .menu-button {
    anchor-name: --menu-button;
  }

  .site-menu {
    position: fixed;
    inset: auto;
    inset-block-start: anchor(--menu-button bottom);
    inset-inline-end: 1rem;
    inline-size: min(80dvw, 24rem);
    max-inline-size: calc(100dvw - 2rem);
    block-size: fit-content;
    max-block-size: calc(100dvh - 6rem);
    margin-block-start: 0.5rem;
    overflow: auto;
  }
}
```

The `anchor()` declaration places the menu below the button; see Fallbacks for browsers without Anchor Positioning. For collision-aware positioning near viewport edges, see `viewport-aware-dropdowns` (via `npx -y modern-web-guidance@latest retrieve "viewport-aware-dropdowns"`).


## Wide Layout: Inline Navigation

When the row fits, hide the collapsed-layout trigger and reset the popover presentation so the same list participates in the header as an inline navigation.

```css
.site-header.is-wide {
  .menu-button {
    display: none;
  }

  .site-menu {
    position: static;
    display: flex;
    align-items: center;
    gap: 0.25rem;
    inline-size: auto;
    max-inline-size: none;
    block-size: auto;
    max-block-size: none;
    overflow: visible;
    margin: 0;
    padding: 0;
    border: 0;
    background: transparent;
    box-shadow: none;
  }

  .site-menu::backdrop {
    display: none;
  }
}
```

CSS changes the presentation, but JavaScript decides which layout applies and must also synchronize the `popover` attribute with it. Measure the inline row: show it inline, compare `scrollWidth` with `clientWidth` on the header's inner row, then toggle `is-wide`. Add a temporary `is-measuring` class that sets `transition: none` on descendants for the measurement and remove it in `requestAnimationFrame`, otherwise the menu animates from its inline styles on every resize and flickers. Keep the navigation as a popover only in the collapsed layout; remove the attribute in the inline layout. Removing it closes an open popover, so note whether the menu was open and call `showPopover()` again if the layout is still collapsed; otherwise a resize closes the open menu. Run one shared function from `ResizeObserver`s on the header and the navigation list, so font loading and text changes are caught without special cases. Remeasure while inline on every notification, but while collapsed only when the header's width has grown. Switching to inline just to measure on every shrink makes the layout thrash. The tradeoff is that a collapsed menu that gets smaller by itself (for example a shorter translation) stays collapsed until the header grows. Do not infer the layout from a hidden trigger.


## Nested Site Navigation

Use a `<button>` with `aria-expanded` and `aria-controls` as the trigger for nested site navigation, followed immediately by the submenu list. DO NOT use `<details>` and `<summary>`: `<summary>` should map to a `button` role, but has not been consistently implemented in browsers, so screen readers announce it inconsistently (for example as "summary" or "disclosure triangle").

One list must be an inline disclosure when narrow and a floating popover when wide. A top-layer popover cannot render in flow, so `popover` is set only in the wide layout, where `popovertarget` opens it natively. Leave `popover` off the submenu in the markup: without script it stays a nested list inside the menu, matching the narrow layout. In the narrow layout a small click handler toggles `hidden` and `aria-expanded`. Hide the narrow submenu from script, not in the markup.

Synchronize the layout state from the same function that handles the navigation list. When inline, set `popover="auto"` on the submenu, clear `hidden`, and let the native `toggle` event drive `aria-expanded`. When narrow, remove `popover`, set `hidden` from script, and let a click handler toggle `hidden` and `aria-expanded`:

```js
const wide = inner.scrollWidth <= inner.clientWidth; // measured with is-wide set
if (wide) submenu.setAttribute("popover", "auto");
else submenu.removeAttribute("popover");
submenu.hidden = !wide;
trigger.setAttribute("aria-expanded", "false");
```

> [!NOTE]
> Use `setAttribute()`/`removeAttribute()`, NOT the `popover` JS property, as it can cause race conditions if popover is used through a polyfill.

Do not add document-level click-outside, Escape or focus-restoration handlers. The native popover provides those interactions in the wide layout, and the narrow disclosure needs only the click handler. The declarative `popovertarget` makes the button the submenu's implicit anchor, so wide-layout positioning can use `position-area` without a separate `anchor-name` or `position-anchor` declaration.

### Hover preview

In the wide layout, open the submenu (`submenu.showPopover({ source: trigger })`) on `pointerenter` of the parent `<li>` when `matchMedia("(hover: hover)").matches` and `event.pointerType !== "touch"`, and hide it on `pointerleave` if it is still only hover-previewed (if the user clicks the trigger while previewing, clear the preview flag and call `event.preventDefault()` so the click pins it open instead of toggling it closed). Do not open it when the trigger receives keyboard focus; only `Enter` or `Space` toggles it. Close the open submenu when focus moves to an element outside the submenu's whole list item (`focusout` with a non-null `relatedTarget` outside it), so tabbing or `Shift`+`Tab`bing away closes it.

Position the wide submenu below its trigger with CSS Anchor Positioning (`position-area: block-end span-inline-end` and `position-try-fallbacks: flip-block, flip-inline`). For collision-aware positioning of a plain popover, see `viewport-aware-dropdowns` (via `npx -y modern-web-guidance@latest retrieve "viewport-aware-dropdowns"`). Add a `::before` bridge across any margin gap above the submenu (with `overflow: visible` on the popover) so moving the pointer from the trigger into the submenu does not fire `pointerleave`, and set `height: auto` to prevent Safari from stretching the positioned popover to its placement area:

```css
.site-header.is-wide {
  .dropdown-list[popover] {
    position: absolute;
    position-area: block-end span-inline-end;
    position-try-fallbacks: flip-block, flip-inline;
    inset: auto;
    height: auto;
    inline-size: max-content;
    min-inline-size: 12rem;
    margin-block-start: 0.5rem;
    overflow: visible;
  }

  .dropdown-list::before {
    content: "";
    position: absolute;
    inset-inline: 0;
    inset-block-end: 100%;
    block-size: 0.5rem;
  }

  /* Also hides the list before script sets popover, so an open narrow disclosure cannot widen the measured row */
  .dropdown-list:not(:is(:popover-open, .\:popover-open)) {
    display: none;
  }

  .dropdown-list:is(:popover-open, .\:popover-open) {
    display: grid;
  }
}
```


## Indicating the Current Page

Use `aria-current="page"` to identify the current page. Set it at build time or with JavaScript, and use `[aria-current="page"]` as the CSS styling hook. The visual treatment is a design decision; do not assume a particular pseudo-element, border, or orientation. Do not indicate the current page with color alone (WCAG 1.4.1). Ensure the color of the visual indicator has a contrast of at least `3:1` with the background color.

## Transitions and Accessibility

If the navigation is animated (optional; add it when the abrupt open and close feels out of place), use `@starting-style`, include `display` and `overlay` in `transition`, and declare `transition-behavior: allow-discrete` as a separate property after `transition` (DO NOT put `allow-discrete` in the `transition` shorthand, which makes older browsers ignore the entire `transition` declaration) for both the mobile menu and the wide floating submenu. Animate only `opacity` and `translate`, and respect `prefers-reduced-motion`. For the reusable transition pattern, see `animate-element-entry-exit` (via `npx -y modern-web-guidance@latest retrieve "animate-element-entry-exit"`).

### Optional: Narrow submenu slide-down

Optional. In the narrow layout the submenu is an inline list toggled with `hidden`, which opens instantly by default and is fully functional that way. To slide it open and closed:

- Keep the `[hidden]` list rendered with `display: grid` and `block-size: 0`, then transition `block-size` to `auto` with `interpolate-size: allow-keywords` and `clip-path: inset(-6px)` to clip the slide without cutting off the focus ring (not `overflow: clip`, which clips the ring; `6px` is an example, use at least the ring's outline width plus offset).
- Set `content-visibility: hidden` on the `[hidden]` list, include `content-visibility` in `transition`, and declare `transition-behavior: allow-discrete` separately after `transition` so the content stays rendered until the close finishes.
- Scope it to the collapsed layout and `prefers-reduced-motion: no-preference`; outside it, `[hidden]` must stay `display: none`. In the wide layout the submenu is a popover and must not be clipped.
- For the general technique, see `animate-to-intrinsic-sizes` (via `npx -y modern-web-guidance@latest retrieve "animate-to-intrinsic-sizes"`).

Use logical properties for sizing, spacing, and positioning. Use `dvw` and `dvh` for viewport-relative limits, provide visible `:focus-visible` indicators, and adapt custom borders and current-page indicators under `forced-colors: active`.


## Fallbacks

### Fallbacks & browser support for Popover

Baseline status for Popover: Newly available. It's been Baseline since 2025-01-27.
Supported by: Chrome 116 (Aug 2023), Edge 116 (Aug 2023), Firefox 125 (Apr 2024), Safari 17 (Sep 2023), and Safari iOS 18.3 (Jan 2025).

The Popover API is mostly **progressive enhancement**, but its defining behaviors — top-layer promotion, light-dismiss, and `popovertarget` invocation — have no CSS-only equivalent. Older browsers need a polyfill, or a manual fallback if you would rather not ship one.

**Polyfill:** To support the `popover` attribute in older browsers, conditionally load [`@oddbird/popover-polyfill`](https://github.com/oddbird/popover-polyfill). **MANDATORY:** Feature detect by checking for the `popover` property on `HTMLElement.prototype`, and load the polyfill **only** when native support is missing — do NOT load it unconditionally.

With a bundler or import map:

```js
// MANDATORY: Feature detect 'popover' on HTMLElement.prototype.
if (!Object.hasOwn(HTMLElement.prototype, "popover")) {
  import("@oddbird/popover-polyfill");
}
```

If not using a package manager, import from a CDN, e.g. `"https://unpkg.com/@oddbird/popover-polyfill@latest/dist/popover.min.js"`.

Caveats when using the polyfill:
- Do NOT use the `popover` IDL attribute for any code that may run before the polyfill is fully loaded. It will not be mirrored as an attribute, the popover will not be picked up by the polyfill, and the property will be shadowing the accessor the polyfill adds forever. Use `setAttribute()`/`removeAttribute()` instead.
- **Styling caveat:** The polyfill cannot define the real `:popover-open` pseudo-class, so it applies a `.\:popover-open` class instead. **MANDATORY:** Combine the two with `:is()` or `:where()`, otherwise browsers that lack `:popover-open` discard the entire rule:

```css
[popover]:is(:popover-open, .\:popover-open) {
  display: block;
}
```

### Fallbacks & browser support for Anchor positioning

Browser support for Anchor positioning: Limited availability.
Supported by: Chrome 157 (Nov 2026), Edge 157 (Nov 2026), and Safari 27 (Sep 2026).
Unsupported in: Firefox.

For the mobile menu, put an absolute fallback declaration before the `anchor()` declaration. Do not wrap this simple fallback in an `@supports` rule: an unsupported `anchor()` declaration is discarded while the preceding declaration remains valid. Without Anchor Positioning the menu still opens as a popover at a fixed offset from the top of the viewport.

```css
.site-header {
  .site-menu {
    /* Example-only offset: use roughly your header height. Browsers without anchor() keep this one. */
    inset-block-start: 4.75rem;
    inset-block-start: anchor(--menu-button bottom);
  }
}
```

For the wide floating submenu, without CSS Anchor Positioning a popover falls back to centered viewport placement, which detaches the submenu from its navbar trigger. Override the submenu to `position: fixed; margin: 0` inside `@supports not (position-area: block-end)`, and when `event.newState === "open" && !CSS.supports("position-area", "block-end")` in the submenu's `toggle` handler, compute `top` and `left` from `trigger.getBoundingClientRect()` and `submenu.getBoundingClientRect()` (placing it below the trigger, flipping above if `top + size.height > innerHeight`, and clamping `left` to `[0, innerWidth - size.width]`).

### Transitions

Baseline status for @starting-style: Newly available. It's been Baseline since 2024-08-06.
Supported by: Chrome 117 (Sep 2023), Edge 117 (Sep 2023), Firefox 129 (Aug 2024), and Safari 17.5 (May 2024).

Baseline status for transition-behavior: Newly available. It's been Baseline since 2024-08-06.
Supported by: Chrome 117 (Sep 2023), Edge 117 (Sep 2023), Firefox 129 (Aug 2024), and Safari 17.4 (Mar 2024).

Browser support for interpolate-size: Limited availability.
Supported by: Chrome 129 (Sep 2024) and Edge 129 (Sep 2024).
Unsupported in: Firefox and Safari.

Baseline status for content-visibility: Newly available. It's been Baseline since 2025-09-15.
Supported by: Chrome 108 (Nov 2022), Edge 108 (Dec 2022), Firefox 130 (Sep 2024), and Safari 26 (Sep 2025).

Gate the optional narrow submenu slide-down rules that override `[hidden]` with `display: grid` behind `@supports (interpolate-size: allow-keywords)`. Without the gate, browsers lacking keyword interpolation snap the list closed instead of sliding; with it, they keep native `[hidden]` behavior. Without `@starting-style` or `transition-behavior: allow-discrete`, the optional popover entry or exit transitions fall back to opening or closing immediately.

Without script nothing adds `is-wide`, so the header stays collapsed: the menu button opens the native popover and the submenu is a nested list. Do not write a separate no-script layout or a `no-js` class.



=============== GUIA: optimize-image-priority ===============

--- Guide for optimize-image-priority ---
# Optimize image priority

Browsers use heuristics to assign loading priorities to images, but these defaults may not always align with your page's Largest Contentful Paint (LCP).

Using `fetchpriority` on an `<img>` element allows you to explicitly signal an image's importance to the browser, ensuring critical images load faster while non-essential ones don't compete for bandwidth.

The `loading=lazy` attribute prevents images from being downloaded at all when sufficiently off-screen which can further help prioritize images.

## How to implement

1. **Identify the LCP image**: Determine which image is the most likely candidate for the Largest Contentful Paint (usually the hero image at the top of the page).
2. **Elevate LCP priority**: Add `fetchpriority="high"` to the `<img>` element for the LCP candidate.
3. **Deprioritize non-critical images**: For images that are part of a secondary UI or are only revealed after user interaction (like mega menus, modals, or off-screen carousel slides), add `fetchpriority="low"`.
4. **Optimize lazy loading**: Never use `loading="lazy"` on the LCP image. For standard below-the-fold images, `loading="lazy"` is sufficient to defer the request until the user scrolls near them. Avoid adding `fetchpriority="low"` to these images, as you want them to load at normal priority once the user scrolls to them. Reserve `fetchpriority="low"` for images that are technically "above the fold" but not initially visible (e.g., hidden carousel slides or mega menus). For these hidden images, it is acceptable to use `loading="lazy"` as well; the browser will handle the request timing while respecting the low priority.
5. **Prefer default priorities**: If an image should have normal loading priority, omit the `fetchpriority` attribute entirely rather than setting it to `auto`. This is a stylistic convention to keep your HTML cleaner while relying on the browser's native heuristics.

## Example code

```html
<!-- Elevate priority for the LCP image -->
<img src="/images/hero-lcp.jpg"
     alt="Main Banner"
     fetchpriority="high"
     width="800" height="400">

<!-- Deprioritize initially hidden images above the fold -->
<!-- Additionally use `loading="lazy"` if there are likely to be many images-->
 <div class="carousel">
     <img src="/images/gallery-alt.jpg"
          alt="Gallery Image 1"
          width="400" height="300">
     <img src="/images/gallery-alt.jpg"
          alt="Gallery Image 2"
          fetchpriority="low"
          loading="lazy"
          width="400" height="300">
<div>

<!-- Deprioritize images revealed only after user interaction -->
<img src="/images/mega-menu-promo.jpg"
     alt="Special Offer"
     fetchpriority="low"
     width="300" height="150">

<!-- Use lazy loading ALONE for standard below-the-fold images -->
<img src="/images/footer-logo.png"
     alt="Footer Logo"
     loading="lazy"
     width="120" height="60">

<!-- Omit fetchpriority for images with standard priority -->
<img src="/images/standard-image.jpg"
     alt="Standard Image"
     width="400" height="300">
```

## Best practices

- **MANDATORY**: Always apply `fetchpriority="high"` to the LCP image.
- **MANDATORY**: Only use `fetchpriority="high"` on at most 1-2 critical images to avoid network contention and diluting the priority boost.
- **MANDATORY**: Use `fetchpriority="low"` for images that are technically "above the fold" but initially hidden (e.g., hidden carousel slides, mega menu images).
- **MANDATORY**: **Do not** use `fetchpriority="low"` on standard below-the-fold images that are already using `loading="lazy"`. These images should load at normal priority once they enter the viewport.
- **RECOMMENDED**: Avoid using `fetchpriority="auto"`. If you want the default priority, omit the attribute entirely to keep your HTML clean.
- **DO NOT** combine `fetchpriority="high"` with `loading="lazy"`.
- **DO NOT** use the deprecated `importance` attribute. It has been replaced by `fetchpriority` and is not supported by any browser.
- **DO NOT** use the deprecated `loading="auto"` value. It was initially supported by Chrome but has been removed and is not supported by any browser.

## Fallback strategy

Baseline status for Fetch priority: Newly available. It's been Baseline since 2024-10-29.
Supported by: Chrome 103 (Jun 2022), Edge 103 (Jun 2022), Firefox 132 (Oct 2024), and Safari 17.2 (Dec 2023).

The `fetchpriority` attribute is a progressive enhancement for the `<img>` element. If a browser does not support it, the attribute is ignored, and the browser uses its default priority heuristics.


=============== GUIA: motion ===============

--- Guide for motion ---
# Motion

- Use `clip-path` and `mask-image` for custom geometric reveals and smooth fade-outs. Both also clip the element's own focus outline, so when the clipped or masked element is focusable (for example, a scroll container with `tabindex="0"`), draw its focus indicator on a parent element that isn't clipped or masked.
- Use **Scroll-Driven Animations** (`animation-timeline: scroll()`) for non-essential scroll-bound effects instead of JS listeners.
- Use **View Transitions** to animate between complex layout states seamlessly.

## Performance

- Prefer to animate `opacity` and `transform` (including individual transform properties, e.g. `translate` instead of `left/right/top/bottom`) to ensure animations stay on the compositor thread.
- Use `transition-behavior: allow-discrete`, `@starting-style`, and (for top-layer elements like `<dialog>` or `[popover]`) `overlay` to animate discrete entry and exit states natively; see `animate-element-entry-exit` (via `npx -y modern-web-guidance@latest retrieve "animate-element-entry-exit"`) and `animate-to-from-top-layer` (via `npx -y modern-web-guidance@latest retrieve "animate-to-from-top-layer"`).

```css
.popover-reveal {
  /* Transition discrete display and top-layer overlay alongside opacity */
  transition:
    opacity ease-out,
    display,
    overlay;
  transition-duration: 0.2s;
  /* Separate after transition so the shorthand doesn't reset it or fail in older browsers */
  transition-behavior: allow-discrete;
}
```

## Accessibility

- **Provide a pause mechanism**: Allow users to stop auto-running carousels, banners, or other persistent animations. Any moving, blinking, or scrolling content that starts automatically, lasts more than five seconds, and is shown alongside other content needs a visible control to pause, stop, or hide it, such as a `<button>` next to the content that works with the keyboard (WCAG 2.2.2 Pause, Stop, Hide). `prefers-reduced-motion` doesn't replace the control, because users who haven't turned on that setting still need to stop the motion. If the button's label changes between "Pause" and "Play", don't also set `aria-pressed`, or screen readers announce a pressed "Play" button.
- **Default to static views**: Consider defaulting to static states and allowing users to opt in to motion.
- **Don't exceed flash limits (three per second)**: Never include rapid light-to-dark flashing. Such effects can cause seizures. This applies whatever the user's motion preference, so don't put flashing content inside `@media (prefers-reduced-motion: no-preference)` either (WCAG 2.3.1 Three Flashes or Below Threshold: nothing flashes more than three times in any one-second period).
- **Keep the final state**: When you turn off an animation, make sure the element still ends in its final state, so content isn't left hidden (for example, at `opacity: 0`) and focus and selection indicators still appear.

### Respect reduced motion

Always respect reduced motion preferences. Choose between `@media (prefers-reduced-motion: no-preference)` and `@media (prefers-reduced-motion: reduce)` based on whether reduced motion removes the animation entirely or only adjusts its parameters:
- Use `@media (prefers-reduced-motion: no-preference)` when the reduced-motion strategy is to **not have the animation at all** (apply the animation only where needed rather than declaring it broadly and canceling it with `animation: none`).
- Use `@media (prefers-reduced-motion: reduce)` when the reduced-motion strategy is to **change animation parameters** (such as slowing down `animation-duration`).

**DO NOT** globally apply `animation-duration: 0.01ms;` as it can cause certain animations to become _more_ jarring. Instead, handle reduced motion on a case-by-case basis by nesting the `@media` query inside the rule so both versions are kept together. Keep non-motion transitions (such as `color` or `background-color` changes on links and buttons) enabled; only dampen or disable spatial movement, scaling, rotation, or persistent animations.

#### Opt in to motion with `@media (prefers-reduced-motion: no-preference)`

When reduced motion should have no animation at all, default to the static state and enable the animation inside `@media (prefers-reduced-motion: no-preference)` instead of setting `animation` and overriding it with `animation: none`:

```css
/* Good: Default to static state and opt in to full motion */
.spinner {
  @media (prefers-reduced-motion: no-preference) {
    animation: spin 1s linear infinite;
  }
}
```

#### Adjust animation parameters with `@media (prefers-reduced-motion: reduce)`

When the animation should still run under reduced motion with gentler parameters, declare the base animation and override only the changed properties inside `@media (prefers-reduced-motion: reduce)`:

```css
/* Good: Slow down the animation for reduced motion instead of canceling it */
progress:not([value]) {
  animation: slide 1s infinite linear;
  @media (prefers-reduced-motion: reduce) {
    animation-duration: 20s;
  }
}
```

## Working with animation and transition events in JavaScript

On both the `AnimationEvent` (`animationstart`, `animationiteration`, `animationend`, `animationcancel`) and `TransitionEvent` (`transitionrun`, `transitionstart`, `transitionend`, `transitioncancel`) interfaces, `event.target` gives you the originating DOM element. To access the specific `Animation` object (`Animation`, `CSSAnimation`, or `CSSTransition`) that triggered the event, use the readonly `event.animation` property.

When `event.animation` is not available, you can usually find the `Animation` instance by calling `event.target.getAnimations()` and filtering by `event.animationName` (or `event.propertyName` for transitions). However, using `event.animation` directly is preferred, in order to avoid several scenarios where this fallback that filters `event.target.getAnimations()` does not work as expected:

- Web Animations API (`element.animate()`) animations do not have an `animationName`, so they cannot be matched by name when filtering `event.target.getAnimations()`.
- Filtering by `animationName` for `AnimationEvent`s is ambiguous when multiple animations with the same name are attached to the same element.
  - Note that filtering on `transitionProperty` for `TransitionEvent`s works as there is only one event per transition property. 
- `event.target` always points to the originating DOM element, so it does not distinguish between animations running on the element itself versus one of its pseudo-elements — whereas `event.animation.effect.pseudoElement` exposes the target pseudo-element directly.

```js
// Works in most basic CSS animation cases, but fails for WAAPI animations,
// duplicate animation names on the same element, or pseudo-elements.
document.addEventListener('animationstart', (event) => {
  const animation = event.target
    .getAnimations()
    .find((anim) => anim.animationName === event.animationName);
  console.log(animation);
});

// PREFERRED: Directly access the exact Animation object via event.animation
document.addEventListener('animationstart', (event) => {
  const animation = event.animation;
  console.log(animation);
});
```

You can feature-detect support using `'animation' in AnimationEvent.prototype` (or `'animation' in TransitionEvent.prototype`).

When reduced motion means an animation or transition never runs (it's only applied inside `@media (prefers-reduced-motion: no-preference)`, set to `none`, or given a zero duration and no delay), `animationend` and `transitionend` never fire. If your code waits for these events to remove or hide an element, continue immediately when `element.getAnimations()` returns an empty array, or keep a short opacity transition instead of removing it.


=============== GUIA: responsive-design ===============

--- Guide for responsive-design ---
# Responsive design

## Overall best practices

- Use `@container` queries to create component-driven responsive layouts that adapt to their parent container's size rather than the viewport. See `size-aware-styling` (via `npx -y modern-web-guidance@latest retrieve "size-aware-styling"`) for more details.
- Use dynamic viewport units (`dvh`, `dvw`) instead of `vh`/`vw` to prevent layout breakage when mobile browser UI elements (like address bars) appear or disappear.
- Use `aspect-ratio` for media elements (like `<img>` and `<video>`) to reserve space during loading and prevent Cumulative Layout Shift (CLS).

For the layout mechanics behind responsive UI (flexbox, grid, container query units, viewport units), see `css-layout` (via `npx -y modern-web-guidance@latest retrieve "css-layout"`).

## Responsive typography

- **DO** combine viewport-relative and font-relative units in `clamp()` for font sizes that scale with the viewport size while ensuring they stay within a desired range. For example, `clamp(2rem, 1rem + 5vw, 4rem)`. Adjust the proportion of viewport-relative and font-relative units to control how quickly the font-size changes. Use font-relative minimum and maximum values, and keep the maximum no more than 2.5 times the minimum. Viewport units don't grow when users zoom, so the text only grows once its minimum overtakes the viewport-based value; with a maximum no more than 2.5 times the minimum, browser zoom, which goes up to 500%, can still resize the text to 200% (WCAG 1.4.4 Resize Text requires text to resize up to 200% without loss of content or functionality).
- **DON'T** use `vw` alone for font-size without `clamp()`, as it can scale text too small or too large on extreme screens.

To scale type and spacing with the container instead of the viewport, see `fluid-scaling` (via `npx -y modern-web-guidance@latest retrieve "fluid-scaling"`).


