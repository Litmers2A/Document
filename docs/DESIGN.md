## Design Requirements

- **Monochrome only** - no accent colors
- **Images → gray boxes** - use `bg-muted` placeholder divs
- **Minimal & reactive** - focus on layout, hierarchy, spacing
- **Follow globals.css** - use existing color tokens (foreground, background, muted, border)
- **Component by component** - implement incrementally
- **No decorative elements** - keep styling clean and restrained

## Typography Hierarchy

> Updated: 2026-01-08 (change-2026-01-08-2011)

### Principle

**Typography hierarchy requires both size AND weight differentiation.** Size alone is insufficient for information-dense interfaces.

### Weight System

| Level | font-weight | Use Case | Examples |
|-------|-------------|----------|----------|
| **Page Title** | `font-bold` (700) | Main page heading | Dashboard title, Page headers |
| **Section Header** | `font-semibold` (600) | Major section divisions | Card titles, Panel headers |
| **Table Header** | `font-medium` (500) | Column headers, Labels | Table columns, Form labels |
| **Body Text** | `font-normal` (400) | Default content | Paragraphs, Descriptions |

### Design Intent

- **Rapid scanning**: Users should identify page structure visually before reading
- **Information hierarchy**: Headings must be visually heavier to establish clear entry points
- **Reduced cognitive load**: Weight contrast enables structural recognition without semantic processing

### Application

Apply systematically across:
- Creator Studio (all pages and components)
- Admin panels (all pages and components)
- Any information-dense interface requiring rapid structure identification

## Design Notes

- Keep styling clean and restrained
- Avoid decorative elements.
- Optimize for later image/content replacement
