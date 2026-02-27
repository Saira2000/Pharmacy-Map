# Pharmacy Locator Design Guidelines

## Design Approach Documentation

**Selected Approach:** Design System + Reference-Based Hybrid

**Justification:** This utility-focused application prioritizes efficiency, clarity, and user task completion. The map-centric interface requires familiar interaction patterns while maintaining visual polish.

**Primary References:**
- Google Maps interface patterns for map controls and markers
- Material Design principles for clean component structure
- Zillow/Redfin for search-to-map workflows

**Core Design Principles:**
1. Map-first hierarchy - the map is the primary interface element
2. Minimal cognitive load - clear visual hierarchy, obvious controls
3. Responsive efficiency - optimized for both desktop and mobile use
4. Data clarity - pharmacy information must be instantly readable

## Typography

**Font Family:** 
- Primary: 'Inter' or 'Roboto' via Google Fonts CDN
- Fallback: system-ui, -apple-system, sans-serif

**Type Scale:**
- Search Input: text-lg (18px) with font-medium weight for visibility
- Map Info Cards (Pharmacy Names): text-base (16px) font-semibold
- Secondary Text (addresses, details): text-sm (14px) font-normal
- Control Labels: text-xs (12px) font-medium uppercase with tracking-wide
- Radius Display: text-2xl (24px) font-bold for the numeric value

**Hierarchy Notes:** Typography must remain highly legible over map backgrounds. All text on the map interface should maintain strong contrast ratios.

## Layout System

**Spacing Primitives:** Consistent use of Tailwind units: **2, 4, 6, 8, 12, 16**
- Micro spacing (gaps, padding): 2, 4
- Component internal spacing: 6, 8
- Section spacing: 12, 16

**Grid Structure:**
```
Desktop Layout:
- Search bar: Fixed top position, full-width with max-w-2xl centered
- Map container: 100% viewport height minus search bar height
- Control panels: Absolute positioned overlays with appropriate margins

Mobile Layout:
- Search bar: Sticky top with reduced padding (p-4)
- Map: Full viewport height minus search and controls
- Radius controls: Bottom sheet/drawer pattern
```

**Container Strategy:**
- Map: w-full h-full (fills available space)
- Search container: max-w-2xl mx-auto with px-4 padding
- Info cards: max-w-sm with rounded-lg borders
- Control buttons: Consistent sizing (h-10 w-10 for icon buttons, h-10 px-4 for text buttons)

## Component Library

### A. Search Interface
**Address Search Bar:**
- Large, prominent input field with rounded-xl border (border-2)
- Icon prefix (search/location icon from Heroicons)
- Autocomplete dropdown with shadow-xl elevation
- Clear button (X icon) visible when text is present
- Height: h-12 for comfortable touch targets
- Padding: px-4 py-3

### B. Map Controls

**Zoom Controls:**
- Vertical button stack positioned top-right with m-4 offset
- Two square buttons (h-10 w-10) with rounded-lg
- Plus/minus icons from Heroicons
- Subtle shadow-md for depth
- Gap of 2 between buttons

**Radius Controls:**
- Horizontal slider component positioned bottom-left
- Card container (rounded-xl, p-4, shadow-lg)
- Slider input with custom styling
- Numeric display above slider showing current radius
- Preset buttons (500m, 1km, 2km, 5km) below slider
- Buttons: rounded-full, px-4, h-8, text-xs

**Reset/Clear Button:**
- Positioned top-left opposite zoom controls
- Rounded-lg with px-4 py-2
- Icon + text layout (arrow-path icon + "New Search")

### C. Map Markers

**Pharmacy Markers:**
- Custom markers using pharmacy logo images
- Marker size: 32x32px (w-8 h-8)
- White circular background (rounded-full) with shadow-md
- Logo centered within white circle, sized at 24x24px (w-6 h-6)
- Active/selected state: scale-110 transform with increased shadow-lg

### D. Information Cards

**Pharmacy Info Cards:**
- Appears on marker click
- Positioned absolute with dynamic placement based on marker location
- Card structure:
  - Container: rounded-xl, p-4, shadow-2xl, max-w-sm
  - Pharmacy logo: w-12 h-12 rounded-lg (top-left)
  - Name: text-base font-semibold (truncate if needed)
  - Address: text-sm (2 lines with line-clamp-2)
  - Rating display: Stars icon + numeric rating (text-sm)
  - Distance: text-xs with location pin icon
  - "Get Directions" button: w-full, rounded-lg, h-10, mt-4
- Close button: absolute top-2 right-2 (w-6 h-6 icon button)

### E. Radius Visualization

**Circle Overlay:**
- Semi-transparent stroke overlay on map
- Stroke width: 2px
- Dashed pattern (stroke-dasharray)
- Fills area lightly to show coverage zone
- Updates dynamically as radius changes

### F. Loading States

**Map Loading:**
- Full-screen overlay with centered spinner
- Spinner: w-12 h-12 animated spin
- Loading text below: "Finding pharmacies..." (text-sm)

**Search Loading:**
- Inline spinner in search bar (w-5 h-5)
- Replaces search icon during address lookup

## Responsive Breakpoints

**Desktop (lg: 1024px+):**
- Search bar: max-w-2xl, centered with generous top margin (mt-8)
- Control panels: Positioned with m-6 margins
- Info cards: max-w-sm, positioned near clicked marker

**Tablet (md: 768px):**
- Search bar: max-w-xl
- Controls: m-4 margins
- Info cards: max-w-xs, may overlay map more prominently

**Mobile (base: < 768px):**
- Search bar: Full width with px-4, sticky top
- Zoom controls: Reduced to m-3 margins
- Radius controls: Bottom sheet drawer that slides up
- Info cards: Full-width bottom sheet with rounded-t-2xl
- Control buttons: Larger touch targets (h-12 w-12 for icon-only)

## Animations

**Essential Only - Minimal Animation:**
- Map marker selection: smooth scale transition (transition-transform duration-200)
- Info card appearance: slide-in from bottom (translate-y-4 to translate-y-0, duration-300)
- Radius circle: smooth stroke animation on change (transition-all duration-500)
- Search autocomplete dropdown: fade-in (opacity transition, duration-150)

**No Animations For:**
- Map panning/zooming (handled by Google Maps)
- Button hovers (standard state changes only)
- Loading spinners use simple rotate animation only

## Icon Library

**Selected Library:** Heroicons (via CDN)
- Search: MagnifyingGlassIcon
- Location: MapPinIcon  
- Plus/Minus: PlusIcon, MinusIcon
- Close: XMarkIcon
- Reset: ArrowPathIcon
- Directions: ArrowTopRightOnSquareIcon
- Star (ratings): StarIcon

## Accessibility Notes

- All interactive controls must have min-h-10 for comfortable touch
- Map controls include aria-labels ("Zoom in", "Zoom out", "Adjust radius")
- Info cards have proper heading hierarchy (h2 for pharmacy name)
- Search input has clear label association and placeholder text
- Focus states: ring-2 ring-offset-2 for keyboard navigation
- Color contrast maintained for all text over backgrounds

## Images

**No hero image required** - this is a utility application where the interactive map serves as the primary visual element. The map fills the viewport and provides all necessary visual interest through real location data and branded pharmacy logos.