# Custom Pharmacy Icons Guide

This guide explains how to add custom branded pharmacy icons to the Pharmacy Locator application.

## Overview

The application currently uses a **red pharmacy cross icon** as a fallback for all pharmacies. To display branded icons for specific pharmacy chains (like Shoppers Drug Mart, Rexall, etc.), you need to:

1. Add the icon image files to the project
2. Update the pharmacy icons configuration file

## Step 1: Add Icon Files

Place your pharmacy brand icon files in the following directory:
```
attached_assets/pharmacy_icons/
```

Example file structure:
```
attached_assets/
  └── pharmacy_icons/
      ├── shoppers-drug-mart.png
      ├── rexall.png
      ├── pharmasave.png
      ├── london-drugs.png
      ├── walmart-pharmacy.png
      └── costco-pharmacy.png
```

### Icon Requirements:
- **Format**: PNG with transparent background (recommended)
- **Size**: 64x64 pixels or larger (will be scaled down automatically)
- **Design**: Simple, recognizable logo that works at small sizes

## Step 2: Update Configuration

Edit the file: `client/src/utils/pharmacyIcons.ts`

For each pharmacy brand, update the `iconUrl` field with the path to your icon:

```typescript
import shoppersIcon from '@assets/pharmacy_icons/shoppers-drug-mart.png';
import rexallIcon from '@assets/pharmacy_icons/rexall.png';

const pharmacyBrands: PharmacyBrand[] = [
  {
    name: 'Shoppers Drug Mart',
    keywords: ['shoppers', 'shoppers drug mart', 'sdm'],
    iconUrl: shoppersIcon  // ← Add this import
  },
  {
    name: 'Rexall',
    keywords: ['rexall'],
    iconUrl: rexallIcon  // ← Add this import
  },
  // ... more brands
];
```

## How It Works

### Brand Matching
The application automatically matches pharmacy names from Google Maps with your branded icons using the `keywords` array. For example:

- A pharmacy named **"Shoppers Drug Mart #1234"** will match `['shoppers', 'shoppers drug mart', 'sdm']`
- A pharmacy named **"Rexall Pharmacy"** will match `['rexall']`

### Fallback Behavior
- **Branded Pharmacies**: Display at 32x32 pixels with their custom icon
- **Independent Pharmacies**: Display at 24x24 pixels with a small red cross icon

## Adding New Pharmacy Brands

To add a new pharmacy brand that isn't in the list:

1. Add the brand's icon file to `attached_assets/pharmacy_icons/`
2. Add a new entry to the `pharmacyBrands` array:

```typescript
{
  name: 'Your Pharmacy Name',
  keywords: ['keyword1', 'keyword2'], // Terms to match in pharmacy names
  iconUrl: yourPharmacyIcon  // Import at the top of the file
}
```

### Keyword Tips:
- Use lowercase keywords
- Include common abbreviations
- Include variations (e.g., "cvs" and "cvs pharmacy")
- Be specific enough to avoid false matches

## Testing Your Icons

After adding icons:

1. Save the files
2. The application will hot-reload automatically
3. Search for an address with known pharmacy locations
4. Verify that:
   - Branded pharmacies show their custom icons
   - Independent pharmacies show the red cross icon
   - Icon sizes are appropriate (not too large or small)

## Troubleshooting

### Icon not appearing?
- Check that the import path is correct
- Verify the keyword matching is working (check console logs)
- Make sure the icon file exists in `attached_assets/pharmacy_icons/`

### Icon too large/small?
- The icon size is controlled in the `getPharmacyIcon()` function
- Branded icons: 32x32 pixels
- Independent icons: 24x24 pixels
- You can adjust these values if needed

### Icon quality issues?
- Use higher resolution source images (64x64 or larger)
- Ensure transparent background for best results
- PNG format is recommended over JPG

## Example: Complete Implementation

```typescript
// At the top of client/src/utils/pharmacyIcons.ts
import shoppersIcon from '@assets/pharmacy_icons/shoppers-drug-mart.png';
import rexallIcon from '@assets/pharmacy_icons/rexall.png';

const pharmacyBrands: PharmacyBrand[] = [
  {
    name: 'Shoppers Drug Mart',
    keywords: ['shoppers', 'shoppers drug mart', 'sdm'],
    iconUrl: shoppersIcon
  },
  {
    name: 'Rexall',
    keywords: ['rexall'],
    iconUrl: rexallIcon
  },
  // ... other brands with iconUrl: undefined will use fallback
];
```

This setup ensures that:
- Shoppers Drug Mart locations show the Shoppers icon
- Rexall locations show the Rexall icon
- All other pharmacies show the red cross fallback icon
