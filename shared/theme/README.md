# BDO Power Apps — Color Theme

Source of truth for the BDO canvas app color palette, derived from
`../BDO_Power_Apps_Color_Scheme_Example.png`.

## Files

| File | Purpose |
|------|---------|
| `BDO_Colors.yaml` | Canonical hex values with descriptions — human-readable reference |
| `BDO_PowerApps_NamedFormulas.yaml` | Paste-ready `App.Formulas` snippet for any canvas app |
| `BDO_ComponentLibrary_Theme.pa.yaml` | Component library definition exposing colors as typed `Color` outputs |

## Quick-start: Named Formula approach (recommended)

This approach adds zero overhead and works in any canvas app without importing a component library.

1. Open the app in **Power Apps Studio**.
2. Select **App** in the tree view → set the property dropdown to **Formulas**.
3. Copy the block inside `formulas:` from `BDO_PowerApps_NamedFormulas.yaml` and paste it into the formula bar.
4. Reference colors anywhere in the app:

```
// Button Fill
Fill = BDOTheme.ButtonFill

// Header bar
Fill = BDOTheme.HeaderBackground

// Input border on hover
BorderColor = BDOTheme.InputBorderFocus

// Required-field asterisk label
Color = BDOTheme.RequiredIndicator
```

## Component Library approach (for shared component reuse)

Use `BDO_ComponentLibrary_Theme.pa.yaml` when you are building a shared component library that other apps will import:

1. In Power Apps Studio, create (or open) a **Component Library**.
2. Import / paste `BDO_ComponentLibrary_Theme.pa.yaml`.
3. Publish the library, then add it as a dependency in each consuming app.
4. Insert one invisible `BDOColorTheme` instance on the first screen and reference:

```
Fill = BDOColorTheme_1.ButtonFill
```

## Color tokens

| Token | Hex | Usage |
|-------|-----|-------|
| `Primary` | `#CC0000` | BDO Red — buttons, chevrons, accents |
| `PrimaryDark` | `#A30000` | Hover / pressed red |
| `PrimaryLight` | `#FF4040` | Focus ring on red elements |
| `HeaderBackground` | `#2B2B2B` | Dark charcoal header bar |
| `HeaderText` | `#FFFFFF` | Text on header |
| `PageBackground` | `#FFFFFF` | Canvas background |
| `Surface` | `#FFFFFF` | Cards, panels |
| `TextPrimary` | `#1A1A1A` | Body copy |
| `TextSecondary` | `#555555` | Helper / subdued text |
| `TextPlaceholder` | `#999999` | Input placeholder |
| `TextLink` | `#0563C1` | Hyperlinks |
| `TextError` | `#CC0000` | Validation errors, disclaimers |
| `TextOnPrimary` | `#FFFFFF` | Text on red background |
| `InputBackground` | `#FFFFFF` | Input / combobox fill |
| `InputBorder` | `#CCCCCC` | Default input border |
| `InputBorderFocus` | `#CC0000` | Focused input border |
| `InputBorderError` | `#CC0000` | Error-state input border |
| `ButtonFill` | `#CC0000` | Primary button background |
| `ButtonFillHover` | `#A30000` | Primary button hover |
| `ButtonText` | `#FFFFFF` | Primary button label |
| `RequiredIndicator` | `#CC0000` | Required field asterisk |
| `InfoIcon` | `#00B4D8` | Teal info / radio icons |
| `Success` | `#107C10` | Success states |
| `Warning` | `#F7C948` | Warning states |
| `Error` | `#CC0000` | Error states |
| `Divider` | `#E0E0E0` | Section separators |
| `BorderSubtle` | `#F0F0F0` | Light outline borders |
| `ScrollbarFill` | `#CCCCCC` | Scroll track |
