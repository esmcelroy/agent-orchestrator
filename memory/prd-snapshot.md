# PRD Snapshot — Home Library Tracker

Extracted design decisions for quick agent reference. When in doubt, defer to this file for color, typography, icon, and component interaction choices.

---

## Color Palette

| Role | oklch value | Tailwind token / CSS var |
|---|---|---|
| Primary (burgundy) | `oklch(0.42 0.12 15)` | `bg-primary` / `--color-primary` |
| Background (paper) | `oklch(0.97 0.012 70)` | `bg-background` / `--color-background` |
| Card surface | `oklch(0.95 0.015 75)` | `bg-card` / `--color-card` |
| Accent (amber) | `oklch(0.68 0.18 55)` | `bg-accent` / `--color-accent` |
| Sage | `oklch(0.72 0.06 145)` | `text-sage` / `--color-sage` |

**Usage guidelines:**
- Burgundy (`primary`) is used for primary actions, active tab indicators, and selected-item borders.
- Paper (`background`) is the page background — warm, off-white, never pure white.
- Card (`card`) is the surface for `LibraryItemCard` and dialog backgrounds — slightly warmer than paper.
- Amber (`accent`) is used sparingly for highlights, badges, and star/wishlist indicators.
- Sage is used for secondary badges (e.g. location tags) and decorative elements.

---

## Typography

### Typefaces
| Role | Family | Source |
|---|---|---|
| Display / headings | Crimson Pro (serif) | Google Fonts |
| Body / labels / metadata | Inter (sans-serif) | Google Fonts |

### Scale
| Element | Family | Size | Weight |
|---|---|---|---|
| H1 (page title) | Crimson Pro | `text-3xl` (1.875rem) | 600 (SemiBold) |
| H2 (section heading) | Crimson Pro | `text-2xl` (1.5rem) | 600 |
| H3 (card title) | Crimson Pro | `text-xl` (1.25rem) | 500 (Medium) |
| Body copy | Inter | `text-base` (1rem) | 400 (Regular) |
| Labels / captions | Inter | `text-sm` (0.875rem) | 400 |
| Metadata / helper text | Inter | `text-xs` (0.75rem) | 400 |
| Buttons | Inter | `text-sm` (0.875rem) | 500 |

---

## Component Interaction States

### Cards (`LibraryItemCard`)
| State | Visual treatment |
|---|---|
| Default | `bg-card` surface, `border border-border` (1px neutral) |
| Hover | Drop shadow `shadow-md`, scale `scale-[1.02]`, transition 150ms ease |
| Selected / active | `border-2 border-primary` (burgundy border replaces neutral) |

### Inputs
| State | Visual treatment |
|---|---|
| Default | Neutral border `border-input`, `bg-background` fill |
| Focus | `ring-2 ring-accent` (2px amber ring), border color shifts to accent |
| Disabled | `opacity-50 cursor-not-allowed` |
| Error | `ring-2 ring-destructive`, helper text in `text-destructive` |

### Badges
| State | Visual treatment |
|---|---|
| Default | `bg-[color]/10` (10% opacity background), solid `text-[color]` |
| Example — media type | `bg-primary/10 text-primary` |
| Example — location | `bg-sage/10 text-sage` |
| Example — condition | `bg-accent/10 text-accent` |

### Buttons
| Variant | Usage | Treatment |
|---|---|---|
| Solid / primary | Main CTA (Add Item, Save) | `bg-primary text-primary-foreground hover:bg-primary/90` |
| Ghost / secondary | Secondary actions (Cancel, filter toggles) | `bg-transparent hover:bg-accent/10 text-foreground` |
| Outlined / destructive | Delete actions | `border border-destructive text-destructive hover:bg-destructive/10` |

### Empty States
| Element | Treatment |
|---|---|
| Container | Centered vertically and horizontally, `text-muted-foreground` |
| Illustration | Muted icon (Phosphor, `size={64}`, `opacity-30`) |
| Heading | `text-lg font-semibold text-foreground` (Crimson Pro) |
| Body | `text-sm text-muted-foreground` (Inter) |
| CTA | Solid primary button, prominent, below body text |

---

## Phosphor Icon → Action Mapping

| Icon component | Action / meaning |
|---|---|
| `Barcode` | Scanning / barcode input trigger |
| `Book` | Book media type |
| `Disc` | Vinyl record media type |
| `GameController` | Video game media type |
| `FilmStrip` | DVD / film media type |
| `MusicNote` | CD / music media type |
| `MagnifyingGlass` | Search |
| `Funnel` | Filters / filter panel toggle |
| `Heart` | Wishlist / add to wishlist |
| `MapPin` | Location / shelf assignment |
| `Seal` | Condition rating |
| `Plus` | Add new item |
| `PencilSimple` | Edit existing item |
| `Trash` | Delete item |
| `X` | Close dialog / dismiss |
| `Check` | Confirm action |

**Rule:** Only use the icon listed above for its designated action. Do not substitute a different Phosphor icon for an action already in this table — it erodes learned UI patterns.
