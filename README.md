# astro-better-nav-bar

Configurable sticky navigation bar for Astro. Includes a logo slot, section title, dropdown menus, theme selector, search button, login link, and CTA button. No Tailwind required -- all styles are plain CSS with configurable custom properties.

## Installation

```
npm install astro-better-nav-bar
```

Import the stylesheet once in your root layout:

```astro
---
import 'astro-better-nav-bar/style.css';
---
```

## Basic usage

```astro
---
import { NavBar } from 'astro-better-nav-bar';
import MyLogo from './MyLogo.astro';
---

<NavBar
  sectionTitle="Docs"
  sectionTitleHref="/docs"
  dropdowns={[
    { label: 'Products', items: [
      { name: 'Overview', href: '/products' },
      { name: 'Pricing', href: '/pricing' },
    ]},
    { label: 'Resources', items: [
      { name: 'Blog', href: '/blog' },
      { name: 'Community', href: '/community' },
    ]},
  ]}
  loginHref="https://account.example.com/"
  ctaHref="/contact"
  ctaLabel="Get a demo"
  showSearch
>
  <MyLogo slot="logo" />
</NavBar>
```

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `logoHref` | `string` | `'/'` | URL the logo links to |
| `logoAriaLabel` | `string` | `'Home'` | Accessible label for the logo link |
| `sectionTitle` | `string` | — | Text shown next to the logo (e.g. "Docs", "Blog") |
| `sectionTitleHref` | `string` | `'/'` | URL the section title links to |
| `dropdowns` | `NavDropdown[]` | `[]` | Dropdown menus; see shape below |
| `showThemeSelector` | `boolean` | `true` | Show the built-in Light/Dark/System theme selector |
| `showSearch` | `boolean` | `false` | Show a search icon button |
| `loginHref` | `string` | — | If provided, renders a login link |
| `loginLabel` | `string` | `'Log In'` | Text for the login link |
| `ctaHref` | `string` | — | If provided, renders a CTA button |
| `ctaLabel` | `string` | `'Get a demo'` | Text for the CTA button |
| `breadcrumbs` | `{ title: string; href: string }[]` | `[]` | Mobile breadcrumb trail shown in the mobile bar |

### NavDropdown shape

```ts
interface NavDropdown {
  label: string;
  items: { name: string; href: string }[];
}
```

Dropdowns with zero items are silently omitted.

## Slots

| Slot | Description |
|------|-------------|
| `logo` | Logo SVG or image. Rendered inside a link to `logoHref`. |
| `sidebar-toggle` | Optional button to open a docs sidebar. Rendered at the left of the mobile bar. |
| `extra-actions` | Additional items injected before the search/theme/login/CTA cluster in the desktop action area. |

## Section title per page

Pass `sectionTitle` and `sectionTitleHref` as props from each layout or page to change the label that appears next to the logo:

```astro
<!-- docs layout -->
<NavBar sectionTitle="Docs" sectionTitleHref="/docs" ...>

<!-- blog layout -->
<NavBar sectionTitle="Blog" sectionTitleHref="/blog" ...>
```

## Search button

When `showSearch` is `true`, a search icon button appears in both the desktop and mobile action areas. The button carries `data-widget="search-button"` and dispatches no events itself -- wire up your own search handler:

```js
document.querySelectorAll('[data-widget="search-button"]').forEach(btn => {
  btn.addEventListener('click', () => openSearch());
});
```

## Theme selector

The built-in theme selector stores the chosen theme in `localStorage` under the key `theme` and toggles the `.dark` class on `<html>`. On desktop it renders as a dropdown with Light, Dark, and System options. On mobile it renders as a single toggle button (moon/sun icon).

`ThemeSelector` is also exported as a standalone component if you need it outside the nav:

```astro
---
import { ThemeSelector } from 'astro-better-nav-bar';
---
<ThemeSelector />
```

## Sidebar toggle

For docs-style layouts with a collapsible sidebar, pass your toggle button via the `sidebar-toggle` slot:

```astro
<NavBar ...>
  <button
    slot="sidebar-toggle"
    data-widget="visibility-button"
    data-element="side-nav"
    aria-label="Show Menu"
  >
    <!-- hamburger icon -->
  </button>
</NavBar>
```

## CSS custom properties

All colors and the max-width are overridable. Set these on `:root` or any ancestor of `.nav-header`:

| Property | Default | Description |
|----------|---------|-------------|
| `--nav-bg` | `#0f172a` | Header background |
| `--nav-border` | `#475569` | Header bottom border |
| `--nav-text` | `#ffffff` | Primary nav text |
| `--nav-text-muted` | `#94a3b8` | Muted text (breadcrumbs, separator) |
| `--nav-hover` | `#6366f1` | Hover color for links and icons |
| `--nav-mobile-item-bg` | `#1e293b` | Mobile dropdown pill background |
| `--nav-mobile-item-border` | `#475569` | Mobile dropdown pill border |
| `--nav-dropdown-bg` | `#ffffff` | Dropdown panel background |
| `--nav-dropdown-bg-dark` | `#0f172a` | Dropdown panel background in dark mode |
| `--nav-dropdown-border` | `#64748b` | Dropdown panel border |
| `--nav-dropdown-text` | `#374151` | Dropdown item text |
| `--nav-dropdown-hover-bg` | `#f1f5f9` | Dropdown item hover background |
| `--nav-dropdown-hover-text` | `#4338ca` | Dropdown item hover text |
| `--nav-cta-bg` | `#f97316` | CTA button background |
| `--nav-cta-hover-bg` | `#ea580c` | CTA button hover background |
| `--nav-cta-text` | `#ffffff` | CTA button text |
| `--nav-max-width` | `90rem` | Maximum width of the desktop bar content |

Example:

```css
:root {
  --nav-hover: #7c3aed;
  --nav-cta-bg: #16a34a;
  --nav-cta-hover-bg: #15803d;
  --nav-max-width: 80rem;
}
```

## Dark mode

The stylesheet uses a `.dark` ancestor class (compatible with Tailwind's `darkMode: 'class'`). The dropdown panels switch background automatically when `.dark` is on `<html>` or any ancestor.

No `@media (prefers-color-scheme: dark)` rules are included. The built-in `ThemeSelector` handles setting the `.dark` class based on `localStorage` and OS preference.

## Mobile breakpoint

The desktop bar is shown at `min-width: 1024px`. Below that, the mobile bar is shown instead. This matches Tailwind's `lg` breakpoint. The breakpoint is not currently configurable via a CSS variable.
