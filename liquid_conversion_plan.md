# GlowGenie: HTML to Shopify Liquid Conversion Plan

## Overview
Convert the existing `glowgenie-landing.html` (single-file, 9 sections) into a Shopify Liquid theme structure with customizable sections and dynamic product integration.

---

## Directory Structure to Create

```
glowgenie-shopify/
├── assets/                         # (empty - CSS stays inline)
├── config/
│   └── settings_schema.json        # Global theme settings (colors, fonts)
├── layout/
│   └── theme.liquid                # Base HTML wrapper
├── sections/
│   ├── header.liquid               # Navigation
│   ├── hero.liquid                 # Hero banner
│   ├── benefits.liquid             # 6 benefit cards
│   ├── video.liquid                # Video embed
│   ├── features.liquid             # 3 feature items
│   ├── story.liquid                # Brand story
│   ├── testimonials.liquid         # 3 testimonial cards
│   ├── cta.liquid                  # Pricing/CTA section
│   └── footer.liquid               # Footer
├── snippets/
│   ├── benefit-card.liquid         # Reusable benefit card
│   ├── feature-item.liquid         # Reusable feature row
│   ├── testimonial-card.liquid     # Reusable testimonial
│   └── social-icons.liquid         # Social media icons
└── templates/
    └── index.json                  # Homepage template
```

---

## Liquid Object Mapping

### Product Object (for CTA section pricing)
| Current HTML | Shopify Liquid |
|--------------|----------------|
| `$79.99` (original) | `{{ product.compare_at_price | money }}` |
| `$49.99` (current) | `{{ product.price | money }}` |
| Product image | `{{ product.featured_image | img_url: 'master' }}` |
| Product title | `{{ product.title }}` |
| Product description | `{{ product.description }}` |
| Add to cart | `{{ product.variants.first.id }}` in form |

### Shop Object
| Purpose | Shopify Liquid |
|---------|----------------|
| Store name | `{{ shop.name }}` |
| Copyright year | `{{ 'now' | date: '%Y' }}` |
| Store URL | `{{ shop.url }}` |

### Section Settings (via schema)
Each section will have a `{% schema %}` block enabling theme editor customization:
- Text inputs for titles, descriptions, taglines
- Image pickers for images
- URL inputs for links
- Blocks for repeatable content (benefits, testimonials, features)

---

## Section-by-Section Conversion

### 1. `layout/theme.liquid`
**Source:** Lines 1-61 (head, global CSS variables)
- DOCTYPE and `<html>` wrapper
- `{{ content_for_header }}` in `<head>` (required by Shopify)
- Global CSS variables from `:root` block (convert to use `{{ settings.color_* }}`)
- Base reset styles
- `{{ content_for_layout }}` in `<body>`
- Scroll-spy JavaScript (lines 1604-1639)

### 2. `sections/header.liquid`
**Source:** Lines 64-192
**Liquid conversions:**
- Logo: `{{ section.settings.logo_text }}` or `{{ shop.name }}`
- Nav links: `{% for block in section.blocks %}` with `block.settings.label` and `block.settings.link`
- CTA button: `{{ section.settings.cta_text }}`

### 3. `sections/hero.liquid`
**Source:** Lines 195-363
**Liquid conversions:**
- Tagline: `{{ section.settings.tagline }}` <!-- placeholder: "Natural Beauty Awaits" -->
- Title: `{{ section.settings.title_line1 }}` + `{{ section.settings.title_accent }}`
- Description: `{{ section.settings.description }}` <!-- placeholder -->
- Image: `{% if section.settings.hero_image %}...{% else %}[Product Image Placeholder]{% endif %}`
- CTAs: `{{ section.settings.cta_primary_text }}`, `{{ section.settings.cta_secondary_text }}`

### 4. `sections/benefits.liquid`
**Source:** Lines 366-521
**Liquid conversions:**
- Section title: `{{ section.settings.title }}`
- Cards via blocks: `{% for block in section.blocks %}`
  - Icon: `{{ block.settings.icon }}` <!-- e.g., leaf, sparkle, rabbit emojis -->
  - Title: `{{ block.settings.title }}`
  - Description: `{{ block.settings.description }}`

### 5. `sections/video.liquid`
**Source:** Lines 524-670
**Liquid conversions:**
- Title: `{{ section.settings.title }}`
- Video: `{{ section.settings.video_url }}` (type: video_url)
- Caption: `{{ section.settings.caption }}` <!-- placeholder -->
- Conditional embed: `{% if section.settings.video_url != blank %}...{% else %}[Video Placeholder]{% endif %}`

### 6. `sections/features.liquid`
**Source:** Lines 673-865
**Liquid conversions:**
- Feature blocks with alternating layout
- Number: `{{ forloop.index | prepend: '0' }}`
- Title: `{{ block.settings.title }}`
- Description: `{{ block.settings.description }}`
- Highlight: `{{ block.settings.highlight }}` <!-- e.g., "15+ Active Botanicals" -->
- Image: `{{ block.settings.image | img_url: 'master' }}` or placeholder

### 7. `sections/story.liquid`
**Source:** Lines 868-1038
**Liquid conversions:**
- Story text: `{{ section.settings.story_text }}` (richtext) <!-- placeholder -->
- Founder name: `{{ section.settings.founder_name }}` <!-- "Sarah Mitchell" -->
- Founder role: `{{ section.settings.founder_role }}`
- Image: `{{ section.settings.story_image | img_url }}` or placeholder
- Badge: `{{ section.settings.years_number }}` + `{{ section.settings.years_text }}`

### 8. `sections/testimonials.liquid`
**Source:** Lines 1041-1224
**Liquid conversions:**
- Testimonial blocks:
  - Rating stars: `{% for i in (1..block.settings.rating) %}...{% endfor %}`
  - Quote: `{{ block.settings.quote }}` <!-- placeholder -->
  - Author: `{{ block.settings.author_name }}`
  - Detail: `{{ block.settings.author_detail }}` <!-- "Verified Buyer" -->
  - Avatar: initials from name or `{{ block.settings.avatar | img_url }}`

### 9. `sections/cta.liquid` (CRITICAL - Product Integration)
**Source:** Lines 1227-1375
**Liquid conversions:**
```liquid
{% assign product = all_products[section.settings.product] %}
{% if product != blank %}
  <span class="cta-price-original">{{ product.compare_at_price | money }}</span>
  <span class="cta-price-current">{{ product.price | money }}</span>
{% else %}
  <!-- Fallback placeholders -->
  <span class="cta-price-original">{{ section.settings.original_price | default: '$79.99' }}</span>
  <span class="cta-price-current">{{ section.settings.current_price | default: '$49.99' }}</span>
{% endif %}
```
- Badge: `{{ section.settings.badge_text }}`
- Guarantee blocks: `{% for block in section.blocks %}`

### 10. `sections/footer.liquid`
**Source:** Lines 1378-1598
**Liquid conversions:**
- Logo: `{{ section.settings.logo_text }}` or `{{ shop.name }}`
- Tagline: `{{ section.settings.tagline }}` <!-- placeholder -->
- Social blocks: platform + URL
- Menu columns: `{% for link in linklists[block.settings.menu].links %}`
- Copyright: `{{ 'now' | date: '%Y' }} {{ shop.name }}`

---

## Placeholder Handling Strategy

For content that needs to be filled in later:
1. **Empty settings with defaults:** `{{ section.settings.description | default: '' }}`
2. **Comments for missing content:** `<!-- TODO: Add actual content -->`
3. **Conditional placeholders for images:**
   ```liquid
   {% if section.settings.image %}
     <img src="{{ section.settings.image | img_url: 'master' }}">
   {% else %}
     <div class="placeholder">[Image Placeholder]</div>
   {% endif %}
   ```

---

## Config: `settings_schema.json`

Global theme settings to replace CSS variables:
- `color_primary` (#2D5A27 green)
- `color_secondary` (#F5E6D3 cream)
- `color_accent` (#8B4513 brown)
- `color_text` (#333333)
- `font_heading` (Georgia)
- `font_body` (Arial)

---

## Files to Create (16 total)

| File | Purpose |
|------|---------|
| `layout/theme.liquid` | Base wrapper with global styles |
| `config/settings_schema.json` | Theme color/font settings |
| `templates/index.json` | Homepage section order |
| `sections/header.liquid` | Navigation |
| `sections/hero.liquid` | Hero banner |
| `sections/benefits.liquid` | Benefits grid |
| `sections/video.liquid` | Video embed |
| `sections/features.liquid` | Features list |
| `sections/story.liquid` | Brand story |
| `sections/testimonials.liquid` | Testimonials |
| `sections/cta.liquid` | CTA with product pricing |
| `sections/footer.liquid` | Footer |
| `snippets/benefit-card.liquid` | Reusable card |
| `snippets/feature-item.liquid` | Reusable feature |
| `snippets/testimonial-card.liquid` | Reusable testimonial |
| `snippets/social-icons.liquid` | Social icons |

---

## Verification

1. **Syntax validation:** Run `theme check` or upload to Shopify partner account
2. **Theme editor test:** Verify all section settings appear and are editable
3. **Product connection:** Test CTA section with an actual Shopify product
4. **Responsive check:** Test mobile/tablet/desktop breakpoints
5. **Placeholder review:** Confirm all `{{ }}` placeholders render correctly when empty

---

## Sources

- [Shopify Liquid Reference](https://shopify.dev/docs/api/liquid)
- [Shopify Product Object](https://shopify.dev/docs/api/liquid/objects/product)
- [Shopify Theme Architecture](https://shopify.dev/docs/storefronts/themes/architecture)
- [Shopify Sections](https://shopify.dev/docs/storefronts/themes/architecture/sections)
- [Shopify Cheat Sheet](https://www.shopify.com/partners/shopify-cheat-sheet)
