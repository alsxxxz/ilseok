# CLAUDE.md

## Project Overview

This repository contains a **Cafe24 e-commerce theme** — a custom storefront skin for a Korean online shop built on the Cafe24 platform. The template system uses Cafe24's proprietary template engine with `{$variable}` syntax for dynamic content, `<!--@directive-->` for includes/imports, and `module="..."` attributes for platform-managed component areas.

## Platform & Template Engine

- **Platform**: Cafe24 (cafe24.com) — Korean SaaS e-commerce platform
- **Template syntax**: `{$variable_name}` for dynamic values, `{$variable|filter}` for filtered output
- **Layout directives**: `<!--@layout(path)-->`, `<!--@css(path)-->`, `<!--@js(path)-->`, `<!--@import(path)-->`, `<!--@define(name)-->`
- **Module system**: `module="module_name"` attributes mark areas controlled by Cafe24's rendering engine — do **not** remove or rename these attributes
- **Conditional display**: `{$variable|display}` is used as a CSS class toggling visibility (`display` filter outputs `displaynone` when false)
- **Number formatting**: `{$variable|numberformat}` formats numbers with locale-aware separators

## Directory Structure (Cafe24 Theme Convention)

```
/
├── layout/
│   └── basic/
│       └── layout.html          # Global layout wrapper
├── product/
│   └── detail.html              # Product detail page template
├── css/
│   └── module/
│       └── product/
│           ├── detail.css       # Product detail styles
│           └── additional.css   # Additional product info styles
├── js/
│   └── module/
│       └── product/
│           ├── detail.js        # Product detail JS
│           ├── product_image.js # Product image gallery JS
│           ├── review.js        # Review board JS
│           └── qna.js           # Q&A board JS
├── coupon/
│   └── coupon_productdetail.html # Coupon popup partial
└── board/
    └── report_popup.html        # Report popup partial
```

## Key Template Variables

### Product Detail (`product/detail.html`)

| Variable | Description |
|---|---|
| `{$name}` | Product name |
| `{$product_no}` | Product number (unique ID) |
| `{$big_img}` | Main product image URL |
| `{$seo_alt_tag}` | SEO-friendly alt text |
| `{$product_detail}` | Product detail HTML content (injected by platform) |
| `{$zoom_param}` | Query params for image zoom page |
| `{$review_list}` / `{$review_write}` | Review list/write page URLs |
| `{$qna_list}` / `{$qna_write}` | Q&A list/write page URLs |
| `{$qna_count}` | Q&A post count |
| `{$action_buy}` / `{$action_basket}` / `{$action_wishlist}` | JS actions for purchase/cart/wishlist |
| `{$soldout_display}` | Controls sold-out state display |
| `{$review_display}` | Controls review section visibility |

### Display Classes Pattern

The platform uses `{$something|display}` as class values. When the condition is false, Cafe24 injects `displaynone` (CSS `display: none`). Always use this pattern for conditional visibility rather than removing markup.

## Module Attributes — Critical Rules

Cafe24 `module="..."` attributes define server-rendered regions. **Never:**
- Remove `module` attributes from elements
- Change the `module` attribute values
- Restructure the DOM hierarchy of `module` containers

The following modules appear in the product detail:
- `product_image` — main/thumbnail image area
- `product_addimage` — additional images list
- `Product_mobileImage` — mobile swipe image gallery
- `product_detail` — overall product detail wrapper
- `product_detaildesign` — product spec table rows
- `product_option` — option selectors
- `product_quantity` — quantity selector
- `product_addoption` — text/file add-on options
- `product_fileoption` — file upload option
- `product_setproduct` — bundled set products
- `product_addproduct` — additional companion products
- `product_action` — buy/cart/wishlist action buttons
- `product_review` — review list
- `product_qna` — Q&A list
- `product_relation` / `product_relationlist` — recommended products
- `product_rental` — rental pricing rows
- `product_regularDiscount` — subscription discount tiers
- `myshop_asyncbenefit` — member benefit info (async loaded)
- `product_Imagestyle` — product badge/icon overlay

## Responsive Layout Conventions

- `.RW` — shown on desktop/wide (hidden on mobile via CSS)
- `.RTMB` — shown on tablet/mobile (hidden on desktop via CSS)
- `.display_tablet_only` — tablet-only visibility class
- `data-ez-module` / `data-ez` — Cafe24 EZ editor identifiers, do not modify

## Page Structure — Product Detail

```
breadcrumb (.section.path)
  └── product_headcategory module

product title (mobile only)

product detail section (.section[module=product_detail])
  ├── .detailArea
  │   ├── .imgArea           ← product images (desktop + mobile variants)
  │   └── .infoArea          ← pricing, options, actions
  │       ├── .headingArea   ← product name, icons
  │       ├── rental table   ← rental pricing (conditional)
  │       ├── .regularDelivery ← subscription options (conditional)
  │       ├── option table   ← option/quantity selectors
  │       ├── .productSet    ← set products (conditional)
  │       ├── .productSet.additional ← add-on products (conditional)
  │       ├── total price area
  │       └── .productAction ← buy/cart/wish buttons (+ fixed mobile bar)
  └── event area

coupon partial (<!--@import-->)

additional info section (.section[module=product_additional])
  ├── tab navigation (.detail_tab)
  ├── #prdDetail    ← product detail HTML + related products
  ├── #prdReview    ← review list (conditional)
  ├── #prdQnA       ← Q&A list (conditional)
  └── #prdInfo      ← shipping/exchange/service info (accordion)
```

## Development Conventions

### HTML
- Korean UI text is written in Korean (한국어) — do not translate to English unless instructed
- Button labels for primary actions use English (`BUY IT NOW`, `CART`, `WISH LIST`, `WRITE`, `LIST`) following the store's design language
- Use semantic HTML; `<h1>` for product name, `<h2>` for section headings, `<table>` with `<caption>` for structured data
- Always include `loading="lazy"` on `<img>` tags; always include `ez-prevent="img"` attribute on `<img>` tags within Cafe24 templates
- Avoid `href="javascript:;"` for new code — prefer `href="#none"` with `onclick` or proper event handling

### CSS
- External styles are loaded via `<!--@css(/path/to/file.css)-->` directives at the top of templates
- Class naming: BEM-like with camelCase (e.g., `detailArea`, `imgArea`, `prdList__item`)
- Responsive breakpoints handled via media queries in the CSS files
- Utility classes: `displaynone`, `cboth`, `txtBreak`, `txtEm`, `txtByte`, `pdt150`

### JavaScript
- External scripts loaded via `<!--@js(/path/to/file.js)-->` directives
- jQuery is available globally (Cafe24 platform provides it)
- Swiper.js is used for carousels (e.g., related product slider with `.swiper-container`)
- Platform-provided JS handles option selection, quantity controls, and cart actions via `onclick="{$action_*}"` injected attributes

## Common Customization Patterns

### Hiding a section without breaking platform functionality
Use CSS to hide elements instead of removing markup with `module` attributes:
```css
.section-to-hide { display: none; }
```

### Removing buttons/links that are pure UI
Buttons and links without `module` attributes (e.g., LIST/WRITE nav links, the `board_title` buttons) can be safely removed from HTML.

### Adding custom sections
Place custom HTML outside of `module="..."` containers. Use `data-ez` attributes only if integrating with the Cafe24 EZ editor.

### Conditional sections
Wrap optional content in an element with `class="{$condition_variable|display}"` to let the platform show/hide it.

## Git Workflow

- Branch: `claude/claude-md-mm1g0ru2ltawhm4i-tfQ6u`
- Remote: `http://local_proxy@127.0.0.1:21462/git/alsxxxz/ilseok`
- Commit messages should be descriptive and in English
- Push with: `git push -u origin <branch-name>`

## Notes for AI Assistants

- This is a template file system — variables like `{$name}` are resolved server-side by Cafe24, not in JS
- Do not attempt to add JavaScript logic to resolve `{$variable}` patterns — they are server-rendered
- The `module="..."` system means the platform injects repeated `<li>` or `<tr>` elements at runtime; the static HTML only shows one or two representative items as structure
- Comments in the format `<!-- $variable_name = value -->` inside module containers are configuration directives for the Cafe24 module, not regular HTML comments — preserve them
