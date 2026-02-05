# Hero CTA Block

## Overview

The Hero CTA block is an editorial hero component designed for bold lifestyle imagery and a stacked call-to-action set. It respects the page container width, supports alignment and height variants via data attributes, and renders a readable gradient overlay on top of the image for optimal text legibility.

## Integration

### Block Configuration

| Configuration Key | Type | Default | Description | Required | Side Effects |
|-------------------|------|---------|-------------|----------|--------------|
| `data-align` | string | `right` | Horizontal alignment of content (`left`, `center`, `right`) | No | Changes gradient direction and content alignment |
| `data-vertical` | string | `bottom` | Vertical positioning of content (`top`, `middle`, `bottom`) | No | Adjusts content placement within hero |
| `data-size` | string | `tall` | Hero height variant (`short`: 360px, `tall`: 640px) | No | Changes minimum height of hero |
| `data-gradient-intensity` | string | `medium` | Overlay darkness (`light`: 30%, `medium`: 55%, `strong`: 70%) | No | Controls text readability over images |
| `data-button-style` | string | `default` | Button border radius (`default`: 8px, `pill`: 32px, `sharp`: 0px) | No | Changes button visual style |
| `data-button-width` | string | `medium` | Button width preset (`narrow`: 180px, `medium`: 200px, `wide`: 260px) | No | Controls button width |
| `data-image-quality` | string | `medium` | Image compression quality (`low`, `medium`, `high`) | No | Affects image file size and visual quality |
| `data-image-max-width` | number | `2400` | Maximum image width in pixels (`1200`, `2000`, `2400`, `3000`) | No | Determines largest image breakpoint |
| Interval row | number | `5000` | Milliseconds between slide transitions | No | Controls auto-rotation speed |

**Styling Philosophy:** The block provides structural layout (positioning, gradient overlay, responsive behavior) while respecting global typography, colors, and content styling from your theme and DA.live editor.

### Authoring Structure

Use a three-column row per slide:

| Hero CTA | | |
|---|---|---|
| ![Alt text](/media/hero.jpg) | Shop Men's | Men's Collection |
| ![Alt text](/media/hero2.jpg) | Shop Women's | Women's Collection |

**Column details:**
- **Column 1**: Hero background image - supports multiple formats:
  - Direct image upload in DA.live
  - Image URL as text: `https://example.com/hero.jpg`
  - Link to image: `[Image](https://example.com/hero.jpg)`
  - DAM asset link or reference
  - Existing `<picture>` or `<img>` elements
- **Column 2**: Button text and URLs (format: `Text|URL`)
  - `Shop Mens|/men` - Button text with URL
  - `Shop Women's|/women` - Second button
  - Multiple buttons: one per line
- **Column 3**: Button color variants (one per button)
  - See Button Color Variants section below

Add a final single-cell row to define the interval (ms) between slides:

| Hero CTA |
|----------|
| ... slides ... |
| 5000 |

### Image Optimization

Images are automatically optimized with multiple breakpoints:

| Viewport | Image Width | Target Devices |
|----------|-------------|----------------|
| ≥1920px | 2400px | 4K displays |
| ≥1200px | 2000px | Large desktop |
| ≥768px | 1500px | Tablet/small desktop |
| <768px | 1200px | Mobile |

- **Format**: WebP with fallbacks
- **Optimization**: Medium quality (balanced)
- **Loading**: First slide uses eager loading for LCP optimization
- **Lazy loading**: Subsequent slides load on-demand

### Complete Authoring Example

| Hero CTA | | |
|----------|---|---|
| ![hero-mens.jpg](/media/hero-mens.jpg) | Shop Mens\|/men<br>Shop Women's\|/women | white<br>transparent |
| ![hero-jackets.jpg](/media/hero-jackets.jpg) | Shop Jackets\|/jackets<br>New Arrivals\|/new | brand<br>#FF6B6B |
| 5000 | | |

**Renders as:**
- **Slide 1**: Hero with "Shop Mens" (white button) and "Shop Women's" (transparent button)
- **Slide 2**: Hero with "Shop Jackets" (brand gray button) and "New Arrivals" (custom red button)
- **Rotation**: 5 seconds between slides

### Content Flexibility

**Column 2** accepts multiple formats:

**Simple text with URL:**
```
Shop Men's|/men
```

**Multiple buttons:**
```
Shop Men's|/men
Shop Women's|/women
Shop Kids|/kids
```

**With rich text (optional):**
```html
<h2>Spring Collection</h2>
<p>Shop the latest styles</p>
```

**DA.live tip:** Use Shift+Enter to create new lines within a single cell for multiple buttons.

### Button Color Variants

Define button colors independently using Column 3 (one color per button line in Column 2).

#### **Predefined Variants**

Use design system tokens for consistent branding:

| Column 3 Value | Background | Text Color | Border | Use Case |
|----------------|------------|------------|--------|----------|
| `transparent` | None | White | White | Default, minimal look |
| `white` | White | Dark gray | White | Primary CTA, high contrast |
| `brand` | Brand gray (#454545) | White | Brand | Branded actions |
| `accent` | Blue (#6978d9) | White | Blue | Special features |
| `dark` | Dark gray (#2b2b2b) | White | Dark | Alternative style |
| `outline-dark` | None | Dark gray | Dark | For light backgrounds |

#### **Custom Hex Colors**

Use any hex color for custom branding:

| Column 3 Value | Result |
|----------------|--------|
| `#FF6B6B` | Custom red background |
| `#4ECDC4` | Custom teal background |
| `#F39C12` | Custom orange background |
| `#9B59B6` | Custom purple background |

**Note:** Custom colors are validated for WCAG 2.1 AA contrast. Low contrast warnings appear in browser console.

#### **Example Authoring**

**Column 2:**
```
Shop Mens|/men
Shop Women's|/women
```

**Column 3:**
```
white
transparent
```

**Result:** First button is white solid, second button is transparent outline.

### Data Attributes

Set data attributes on the block element to control layout (optional overrides):
```html
<div class="hero-cta" data-align="right" data-vertical="bottom" data-size="tall">...</div>
```

**Note:** The block respects global styles and DA.live styling by default. Data attributes provide explicit layout control when needed, but content styling (fonts, colors, text formatting) inherits from your global styles and can be controlled via DA.live's rich text editor.

**Setting configuration via Section Metadata in DA.live:**

Add a Section Metadata table **immediately before** your hero-cta block:

```
| Section Metadata        | |
|-------------------------|---|
| data-align              | center |
| data-vertical           | middle |
| data-size               | tall |
| data-gradient-intensity | medium |
| data-button-style       | pill |
| data-button-width       | wide |
| data-image-quality      | high |
| data-image-max-width    | 2400 |

| Hero CTA | | |
|----------|---|---|
| ...your hero content... |
```

**Important Notes:**
- Section Metadata must be **directly above** the hero-cta block (no other content between)
- All fields are **optional** - only include the ones you want to override
- After adding/changing metadata, **save and preview/publish** in DA.live for changes to take effect
- Changes may take a moment to propagate to the published site

### Complete Example with All Options

```
| Section Metadata        | |
|-------------------------|---|
| data-align              | center |
| data-vertical           | middle |
| data-size               | tall |
| data-gradient-intensity | strong |
| data-button-style       | pill |
| data-button-width       | wide |
| data-image-quality      | high |
| data-image-max-width    | 3000 |

| Hero CTA | | |
|----------|---|---|
| ![hero.jpg](/media/hero.jpg) | Shop Now\|/shop<br>Learn More\|/about | white<br>transparent |
| 8000 | | |
```

**Result:**
- Centered content vertically and horizontally
- Tall hero (640px)
- Strong gradient (70% dark) for maximum text readability
- Pill-shaped buttons (rounded)
- Wide buttons (260px)
- High-quality images up to 3000px
- 8-second slide rotation
- First button: white solid
- Second button: transparent outline

## Behavior Patterns

### Auto-rotation

- **Multiple slides**: Automatically transitions between slides at configured interval
- **Single slide**: No rotation occurs
- **Reduced motion**: Respects `prefers-reduced-motion` user preference (disables auto-rotation)

### Responsive Behavior

| Breakpoint | Min Height | Padding | Max Content Width |
|------------|-----------|---------|-------------------|
| Desktop (>900px) | 560px (default), 640px (tall), 360px (short) | 48px 32px | 420px |
| Tablet (≤900px) | 520px | 32px 20px | 360px |
| Mobile (≤600px) | 460px | 24px 16px | 100% |

### Layout & Design

- **Container width**: Respects page max-width (default behavior)
- **Border radius**: Uses design system tokens (`var(--shape-border-radius-2)`)
- **Gradient overlay**: Ensures text legibility
  - **Right align**: Gradient from right to left (default)
  - **Left align**: Gradient from left to right
  - **Center align**: Radial gradient from center

### User Interaction Flows

1. **Page Load**: First slide displays immediately; auto-rotation begins after configured interval
2. **CTA Click**: Standard link navigation to configured URL
3. **Category Link**: Optional third column provides additional navigation

### Accessibility Features

- **Keyboard Navigation**: All buttons are keyboard accessible
- **Focus Indicators**: WCAG 2.1 compliant focus outlines (`:focus-visible`)
- **ARIA Labels**: Buttons include `aria-label` for screen readers
- **Reduced Motion**: Respects `prefers-reduced-motion` preference (disables auto-rotation)
- **Alt Text**: Images support alt text from source
- **Color Contrast**: Custom hex colors validated for WCAG AA compliance (4.5:1 minimum)
- **Disabled States**: Placeholder buttons (`href="#"`) marked with `aria-disabled`

### Error Handling

- **Missing images**: Slide renders without media element, shows content only
- **Invalid interval**: Falls back to 5000ms default
- **Empty content**: Gracefully handles missing or empty cells
- **Missing color variant**: Defaults to transparent
- **Invalid hex color**: Falls back to transparent variant
- **Low contrast**: Warning logged to console (doesn't block rendering)

## Section Metadata Reference

### Complete Metadata Options

| Metadata Key | Values | Default | Effect |
|--------------|--------|---------|--------|
| `data-align` | `left`, `center`, `right` | `right` | Content horizontal position & gradient direction |
| `data-vertical` | `top`, `middle`, `bottom` | `bottom` | Content vertical position |
| `data-size` | `short`, `tall` | `tall` | Hero height (360px / 640px) |
| `data-gradient-intensity` | `light`, `medium`, `strong` | `medium` | Overlay darkness (30% / 55% / 70%) |
| `data-button-style` | `default`, `pill`, `sharp` | `default` | Border radius (8px / 32px / 0px) |
| `data-button-width` | `narrow`, `medium`, `wide` | `medium` | Button width (180px / 200px / 260px) |
| `data-image-quality` | `low`, `medium`, `high` | `medium` | Image compression quality |
| `data-image-max-width` | `1200`, `2000`, `2400`, `3000` | `2400` | Max image size (pixels) |

### Gradient Intensity Examples

**Light (30% opacity):**
- Use for high-contrast images
- Minimal overlay, image-forward
- Best when text is naturally readable

**Medium (55% opacity):**
- Balanced approach (default)
- Works for most images
- Good text legibility

**Strong (70% opacity):**
- Maximum readability
- Use for busy/complex images
- Accessibility-focused

### Button Style Examples

**Default (8px radius):**
- Modern, friendly appearance
- Matches design system default
- Balanced corners

**Pill (32px radius):**
- Fully rounded ends
- Contemporary, approachable
- Softer aesthetic

**Sharp (0px radius):**
- Square corners
- Bold, technical feel
- Minimalist design

### Button Width Examples

**Narrow (180px):**
- Compact appearance
- Good for short text ("Shop" / "Buy")
- More refined look

**Medium (200px):**
- Balanced size (default)
- Works for most text lengths
- Current setting

**Wide (260px):**
- Prominent CTA
- Better for longer text
- More impact

### Common Configuration Presets

#### **Minimal Hero**
```
| Section Metadata        | |
|-------------------------|---|
| data-gradient-intensity | light |
| data-button-style       | pill |
| data-button-width       | narrow |
```
Clean, understated design with subtle overlay.

#### **Impact Hero**
```
| Section Metadata        | |
|-------------------------|---|
| data-size               | tall |
| data-gradient-intensity | strong |
| data-button-width       | wide |
```
Maximum prominence with large buttons and dark overlay.

#### **Centered Statement**
```
| Section Metadata | |
|------------------|---|
| data-align       | center |
| data-vertical    | middle |
| data-button-style | pill |
```
Centered content for bold, direct messaging.

#### **Performance Focused**
```
| Section Metadata     | |
|----------------------|---|
| data-image-quality   | low |
| data-image-max-width | 1200 |
```
Faster loading for mobile-first experiences.

## Performance Optimization

### Largest Contentful Paint (LCP)

- **Eager loading**: First slide image loads immediately (no lazy loading)
- **Optimized formats**: WebP with JPEG/PNG fallbacks
- **Responsive sizing**: Serves appropriate image sizes per viewport
- **Skeleton loading**: Visual feedback during image load

### Core Web Vitals

- **LCP Target**: <2.5s (first slide optimized for this metric)
- **CLS**: Minimal layout shift with fixed heights
- **FID**: Smooth interactions with CSS transitions
- **GPU Acceleration**: Uses `opacity` and `transform` for animations

## Advanced Customization

### Making the Hero Full-Bleed

If you want the hero to span the full viewport width (edge-to-edge), add this CSS:

```css
.hero-cta-container .hero-cta-wrapper {
  max-width: unset;
  padding: 0;
}
```

Add this to your project's custom CSS or in a `<style>` block on the page.

## Troubleshooting

### Images not displaying

Check that Column 1 contains valid image references:

**Valid formats:**
```
✅ Direct image upload in DA.live
✅ https://example.com/image.jpg
✅ /media/hero.jpg
✅ [Link](https://dam.example.com/hero.png)
```

**Debug in console:**
```javascript
// Check if images were found
document.querySelectorAll('.hero-cta-media img')

// Check slide structure
document.querySelector('.hero-cta-slide')?.innerHTML
```

### Block not rendering

1. Check the block exists: `document.querySelector('.hero-cta')`
2. Verify JavaScript loaded without errors (Console tab)
3. Check CSS is applied: Network tab → `hero-cta.css`
4. Verify table has at least 2 rows (slides + interval row)
