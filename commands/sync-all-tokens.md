# Sync All Tokens to Figma

Extract all design tokens from the app codebase and sync them to Figma in one command.

## Usage

When you run `/sync-all-tokens`, the assistant will:

1. Analyze all token definitions across the codebase
2. Organize into categories (Colors, Typography, Spacing, Size, Radius, Border, Elevation)
3. Create or update Figma variables and text styles with Light/Dark modes

Example: `/sync-all-tokens`

## Token Categories

### **Colors**
- Neutral, Primary, Success, Warning, Error
- Light/Dark mode support
- Format: `neutral-100`, `primary-500`

### **Typography**
- Font families, sizes, weights, line heights
- Text style naming: `{Category}/font-{category}-{size}`
- Example: `Body/font-body-md`

### **Spacing**
- Layout and component spacing scale
- Format: `spacing-1`, `spacing-4`, `spacing-8`
- Values: 4px, 8px, 16px, 24px, 32px, etc.

### **Size**
- Icon sizes, avatar sizes, component dimensions
- Format: `size-sm`, `size-md`, `size-lg`
- Example: `icon-size-md: 24px`

### **Radius**
- Border radius values
- Format: `radius-none`, `radius-sm`, `radius-md`, `radius-full`
- Values: 0px, 4px, 8px, 16px, 9999px

### **Border**
- Border width values
- Format: `border-1`, `border-2`, `border-4`
- Values: 1px, 2px, 4px

### **Elevation**
- Shadow primitives for depth
- Format: `elevation-low`, `elevation-medium`, `elevation-high`
- Box-shadow definitions

## File Sources

Search these files for token definitions:
- `src/app/globals.css` - CSS custom properties
- `tailwind.config.ts` - Tailwind configuration
- `src/styles/*.css` - Additional stylesheets
- Component style files with inline definitions

## Token Value Formats

### Colors
- Hex: `#3B82F6`, `#fff`
- RGB: `rgb(59, 130, 246)`
- HSL: `hsl(217, 91%, 60%)`
- CSS Variables: `var(--color-primary-500)`

### Spacing & Sizes
- Pixels: `16px`, `24px`
- Rem: `1rem`, `1.5rem`
- CSS Variables: `var(--spacing-4)`

### Shadows (Elevation)
- Box-shadow: `0 1px 3px rgba(0, 0, 0, 0.1)`
- Multiple shadows: `0 1px 2px rgba(0,0,0,0.05), 0 2px 4px rgba(0,0,0,0.05)`

## Instructions

1. **Scan Codebase**
   - Read CSS files for all token variable definitions
   - Extract Tailwind config values
   - Identify token patterns and naming conventions
   - Group by category

2. **Organize Tokens**
   ```javascript
   const tokens = {
     colors: {
       neutral: { 100: '#F5F5F5', 200: '#E5E5E5', ... },
       primary: { 500: '#3B82F6', 600: '#2563EB', ... }
     },
     spacing: {
       1: '4px',
       2: '8px',
       4: '16px',
       8: '32px'
     },
     size: {
       sm: '16px',
       md: '24px',
       lg: '32px'
     },
     radius: {
       none: '0px',
       sm: '4px',
       md: '8px',
       lg: '16px',
       full: '9999px'
     },
     border: {
       1: '1px',
       2: '2px',
       4: '4px'
     },
     elevation: {
       low: '0 1px 3px rgba(0, 0, 0, 0.1)',
       medium: '0 4px 6px rgba(0, 0, 0, 0.1)',
       high: '0 10px 15px rgba(0, 0, 0, 0.1)'
     }
   };
   ```

3. **Map Dark Mode Values**
   - **Colors:** Invert neutrals, adjust brand colors
   - **Spacing:** Same across modes
   - **Size:** Same across modes
   - **Radius:** Same across modes
   - **Border:** Same across modes
   - **Elevation:** Adjust shadow intensity for dark backgrounds

4. **Connect to Figma**
   - Use figma-friend plugin
   - Verify `figma.variables` and `figma.createTextStyle()` APIs available

5. **Create/Update Variables & Styles**

   **For Colors, Spacing, Size, Radius, Border, Elevation:**
   ```javascript
   const collection = figma.variables.getLocalVariableCollections()
     .find(c => c.name === categoryName) ||
     figma.variables.createVariableCollection(categoryName);

   const variable = figma.variables.createVariable(
     tokenName,
     collection,
     tokenType // 'COLOR', 'FLOAT', etc.
   );

   // Set Light mode value
   variable.setValueForMode(lightModeId, value);

   // Set Dark mode value (if applicable)
   if (darkModeValue) {
     variable.setValueForMode(darkModeId, darkModeValue);
   }
   ```

   **For Typography:**
   ```javascript
   const textStyle = figma.createTextStyle();
   textStyle.name = `${category}/font-${category}-${size}`;
   textStyle.fontName = { family: 'Inter', style: fontWeight };
   textStyle.fontSize = fontSize;
   textStyle.lineHeight = { value: lineHeight, unit: 'PIXELS' };
   ```

6. **Report Results**
   - List created/updated items per category
   - Show any skipped or conflicting tokens
   - Summary statistics

## Output Format

```
✅ Synced all tokens to Figma

Colors (42 variables):
  Created: neutral-50 through neutral-950, primary-500, primary-600
  Updated: success-500 (Dark mode adjusted)

Typography (12 text styles):
  Created: Display/font-display-lg, Body/font-body-md
  Updated: Headline/font-headline-md (font weight changed)

Spacing (8 variables):
  Created: spacing-1, spacing-2, spacing-4, spacing-8

Size (6 variables):
  Created: size-xs, size-sm, size-md, size-lg, size-xl

Radius (5 variables):
  Created: radius-none, radius-sm, radius-md, radius-lg, radius-full

Border (3 variables):
  Created: border-1, border-2, border-4

Elevation (3 variables):
  Created: elevation-low, elevation-medium, elevation-high

Configured Light/Dark modes ✅
Total: 79 tokens synced
```

## Example

**Input (globals.css):**
```css
:root {
  /* Colors */
  --color-neutral-100: #F5F5F5;
  --color-primary-500: #3B82F6;

  /* Spacing */
  --spacing-4: 16px;
  --spacing-8: 32px;

  /* Size */
  --size-md: 24px;

  /* Radius */
  --radius-md: 8px;

  /* Border */
  --border-1: 1px;

  /* Elevation */
  --elevation-low: 0 1px 3px rgba(0, 0, 0, 0.1);
}
```

**Output (Figma):**
```
Variables
├── Colors
│   ├── neutral-100 (Light: #F5F5F5, Dark: #262626)
│   └── primary-500 (Light: #3B82F6, Dark: #60A5FA)
├── Spacing
│   ├── spacing-4 (16px)
│   └── spacing-8 (32px)
├── Size
│   └── size-md (24px)
├── Radius
│   └── radius-md (8px)
├── Border
│   └── border-1 (1px)
└── Elevation
    └── elevation-low (shadow)

Text Styles
└── [Typography styles from component files]
```

## Edge Cases

- **Duplicate definitions:** Use the last defined value
- **Invalid token values:** Skip and report warning
- **Missing Dark mode:** Generate based on Light mode rules (colors only)
- **Existing variables:** Update values, preserve references
- **Font not available:** Report warning, skip text style

## Tailwind Class Mappings

### Spacing
- `p-1`: 4px → `spacing-1`
- `p-2`: 8px → `spacing-2`
- `p-4`: 16px → `spacing-4`
- `p-8`: 32px → `spacing-8`

### Border Radius
- `rounded-none`: 0px → `radius-none`
- `rounded-sm`: 2px → `radius-sm`
- `rounded`: 4px → `radius-md`
- `rounded-lg`: 8px → `radius-lg`
- `rounded-full`: 9999px → `radius-full`

### Border Width
- `border`: 1px → `border-1`
- `border-2`: 2px → `border-2`
- `border-4`: 4px → `border-4`

### Shadows
- `shadow-sm` → `elevation-low`
- `shadow` → `elevation-medium`
- `shadow-lg` → `elevation-high`
