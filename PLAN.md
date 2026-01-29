# GlowGenie Single-Page Landing Page - Implementation Plan

## 1. File Structure
```
glowgenie-website/
├── glowgenie-landing.html    # Single self-contained file (all CSS/JS inline)
└── README.md                 # Development instructions (optional)
```

That's it - one HTML file as requested for Shopify Custom Liquid compatibility.

---

## 2. Modularity Approach

### Section Independence Strategy

Each section will be a completely self-contained HTML block following this pattern:
```html
<!-- ============================================
     SECTION: [Name]
     ============================================ -->
<section id="[section-id]" class="[section]-section">
  <style>
    /* All styles scoped with .[section]-* prefix */
    .[section]-container { }
    .[section]-title { }
    .[section]-content { }
  </style>

  <!-- Section HTML content -->
</section>
```

### Naming Convention (BEM-inspired, section-scoped)

| Section      | Class Prefix    | Example Classes                          |
|--------------|-----------------|------------------------------------------|
| Menu         | `.menu-*`       | `.menu-nav`, `.menu-link`, `.menu-logo`  |
| Hero         | `.hero-*`       | `.hero-container`, `.hero-title`, `.hero-cta` |
| Benefits     | `.benefits-*`   | `.benefits-grid`, `.benefits-card`       |
| Video        | `.video-*`      | `.video-wrapper`, `.video-title`         |
| Features     | `.features-*`   | `.features-list`, `.features-item`       |
| Story        | `.story-*`      | `.story-content`, `.story-image`         |
| Testimonials | `.testimonials-*` | `.testimonials-slider`, `.testimonials-quote` |
| CTA          | `.cta-*`        | `.cta-button`, `.cta-heading`            |
| Footer       | `.footer-*`     | `.footer-links`, `.footer-copyright`     |

### Reorderability

- Each section block can be cut/pasted to any position
- No section references another section's classes
- Shared styles (CSS variables, base resets) in a single `<style>` block at the top
- Section order in HTML = visual order on page

---

## 3. Performance Optimization Strategy

### CSS Performance

- CSS Variables for colors/fonts (defined once, used everywhere)
- No external stylesheets - all inline (eliminates HTTP requests)
- Minimal selectors - max 2-3 levels deep (e.g., `.hero-content .hero-title`)
- No unused styles - each section only has its own styles
- Target: <5KB per section (easily achievable with scoped styles)

### HTML Performance

- Semantic elements: `<section>`, `<nav>`, `<article>`, `<figure>`
- Lazy loading for images: `loading="lazy"`
- Proper image sizing: width and height attributes to prevent layout shift

### JavaScript Performance

- Minimal JS - only for scroll-spy active states (optional)
- No frameworks or libraries
- Deferred execution - script at bottom or defer attribute

### Loading Strategy

- Single file = single request
- CSS at top (in `<head>` or inline with sections)
- JS at bottom (non-blocking)

---

## 4. CSS Organization

### Global Styles Block (at top of file)
```css
:root {
  /* Color Palette - placeholder, customize later */
  --color-primary: #2D5A27;      /* Green */
  --color-secondary: #F5E6D3;    /* Cream */
  --color-accent: #8B4513;       /* Brown */
  --color-text: #333333;
  --color-text-light: #666666;
  --color-white: #FFFFFF;

  /* Typography */
  --font-heading: 'Georgia', serif;
  --font-body: 'Arial', sans-serif;

  /* Spacing Scale */
  --space-xs: 0.5rem;
  --space-sm: 1rem;
  --space-md: 2rem;
  --space-lg: 4rem;
  --space-xl: 6rem;

  /* Breakpoints (for reference) */
  /* Mobile: < 768px */
  /* Tablet: 768px - 1024px */
  /* Desktop: > 1024px */
}

* { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body { font-family: var(--font-body); color: var(--color-text); }
```

### Section-Specific Styles

Each section contains its own `<style>` block immediately inside the `<section>` tag, making sections fully portable.

---

## 5. JavaScript Requirements

### Required JS: None for core functionality

CSS `scroll-behavior: smooth` handles smooth scrolling natively.

### Optional Enhancement: Scroll-Spy for Active Navigation
```javascript
// ~30 lines, runs on scroll
// Highlights current section link in menu
// Can be removed without breaking anything
```

### Decision: Start with CSS-only

- Build without JS first
- Add scroll-spy enhancement after core is working
- Keep JS under 50 lines total

---

## 6. Smooth Scrolling Navigation

### Implementation (CSS-only)
```css
html {
  scroll-behavior: smooth;
}
```

### Menu Links
```html
<nav class="menu-nav">
  <a href="#hero" class="menu-link">Home</a>
  <a href="#benefits" class="menu-link">Benefits</a>
  <a href="#video" class="menu-link">Video</a>
  <a href="#features" class="menu-link">Features</a>
  <a href="#story" class="menu-link">Story</a>
  <a href="#testimonials" class="menu-link">Testimonials</a>
  <a href="#cta" class="menu-link">Shop Now</a>
</nav>
```

### Scroll Offset for Fixed Menu
```css
section[id] {
  scroll-margin-top: 80px; /* Height of fixed menu */
}
```

---

## 7. Responsive Breakpoints

| Breakpoint     | Target  | Layout Strategy                 |
|----------------|---------|----------------------------------|
| < 768px        | Mobile  | Single column, stacked elements |
| 768px - 1024px | Tablet  | 2 columns where appropriate     |
| > 1024px       | Desktop | Full multi-column layouts       |

Each section will include media queries within its own `<style>` block.

---

## 8. Incremental Build Order

### Stage 1: Foundation + Menu
- Base HTML structure
- CSS variables and reset
- Fixed menu bar with navigation links
- **Test:** Menu displays, links work (scroll to empty sections)

### Stage 2: Hero Section
- Full-viewport hero with placeholder content
- Responsive layout
- **Test:** Hero displays below menu, responsive

### Stage 3: Benefits Section
- Grid/card layout
- Placeholder benefit items
- **Test:** Section accessible via menu link

### Stage 4: Video Section
- Video placeholder/embed container
- Responsive video wrapper
- **Test:** Maintains aspect ratio on resize

### Stage 5: Features Section
- Feature list/grid layout
- Icon placeholders
- **Test:** Layout works across breakpoints

### Stage 6: Story Section
- Text + image layout
- Storytelling format
- **Test:** Content flows properly

### Stage 7: Testimonials Section
- Quote cards/carousel placeholder
- Attribution styling
- **Test:** Quotes display correctly

### Stage 8: CTA Section
- Strong call-to-action layout
- Button styling
- **Test:** Visually prominent

### Stage 9: Footer
- Links, copyright, social placeholders
- Final section styling
- **Test:** Anchors to bottom

### Stage 10: Polish & Enhancement
- Add scroll-spy JS (optional)
- Fine-tune transitions
- Final responsive testing
- **Test:** Full page flow, all links work

---

## 9. Development Setup
```bash
# Navigate to project
cd /Users/vinethsiriwardana/Documents/Projects/glowgenie-website

# Start local server
python3 -m http.server 8000

# View in browser
# http://localhost:8000/glowgenie-landing.html
```

---

## 10. Verification Plan

### After each stage:
1. Run Python server: `python3 -m http.server 8000`
2. Open `http://localhost:8000/glowgenie-landing.html`
3. Test menu navigation (smooth scroll)
4. Test responsive layouts (resize browser / dev tools)
5. Check console for errors
6. Verify section can be moved without breaking

### Final testing:
- Test all anchor links
- Test on mobile viewport
- Verify Shopify paste compatibility (valid HTML)
- Check page load performance (should be instant)

---

## Summary

| Aspect           | Approach                                      |
|------------------|-----------------------------------------------|
| File structure   | Single HTML file                              |
| Modularity       | Section-scoped styles, unique class prefixes  |
| Performance      | Inline CSS, minimal JS, lazy loading          |
| CSS organization | CSS variables + BEM-style scoped classes      |
| JavaScript       | CSS-only smooth scroll; optional scroll-spy   |
| Navigation       | Anchor links + scroll-behavior: smooth        |

Ready to build incrementally, one section at a time.