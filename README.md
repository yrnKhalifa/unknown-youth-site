# UNLISTED YOUTH STREETWEAR

Official website for **UNLISTED YOUTH STREETWEAR**, a Ghanaian clothing brand making tees, armless tops, baggy jeans, jackets and socks. *Off the list.*

A single-file, no-build storefront: one `index.html` with all styles, scripts, the logo and product photos embedded. Orders are placed through WhatsApp and paid by Mobile Money or cash on delivery.

---

## Contents

- [Features](#features)
- [Tech stack](#tech-stack)
- [Project structure](#project-structure)
- [Getting started](#getting-started)
- [Configuration](#configuration)
- [Managing products](#managing-products)
- [Editing content](#editing-content)
- [Newsletter setup (Netlify)](#newsletter-setup-netlify)
- [Privacy and cookie policy](#privacy-and-cookie-policy)
- [Deployment](#deployment)
- [Editing on mobile](#editing-on-mobile)
- [Design system](#design-system)
- [Accessibility and performance](#accessibility-and-performance)
- [Browser support](#browser-support)
- [Pre-launch checklist](#pre-launch-checklist)
- [License](#license)

---

## Features

- **Bold, editorial design:** oversized masthead, numbered sections, serif accents, marquee strip and a magazine-style product grid.
- **Welcome animation:** a paper sheet that rips into pieces on first load (skipped for visitors who prefer reduced motion).
- **Product grid with filters:** All, Tees, Armless, Jeans, Jackets, Socks. Each card has a size selector, optional colour swatches, a limited-run tag and a sold-out state.
- **Shopping bag:** slide-out drawer with quantity controls, running total and a persistent bag (when the visitor accepts the policy).
- **Jacket + socks bundle:** automatic saving applied in the bag.
- **WhatsApp checkout:** one tap builds a pre-filled order message with items, sizes, quantities and totals.
- **Drop countdown:** a live timer to the next drop that hides itself once the date has passed.
- **Size guide, story, values and FAQ:** all editable in plain HTML.
- **Newsletter signup:** Netlify Forms, with an email-app fallback if the request fails.
- **Privacy and cookie policy:** accept or reject banner, a full policy dialog and a footer link to change the choice at any time.
- **Floating WhatsApp button** for quick questions.

## Tech stack

| Area | Choice |
| --- | --- |
| Markup, style, logic | Plain HTML, CSS and vanilla JavaScript (no framework, no build step) |
| Fonts | Big Shoulders Display, Instrument Serif, Work Sans (Google Fonts) |
| Forms | Netlify Forms |
| Ordering | WhatsApp click-to-chat (`wa.me`) |
| Storage | Browser `localStorage` (bag and policy choice only) |
| Hosting | Any static host; set up for Netlify |

## Project structure

```
.
├── index.html   # the entire site (HTML + CSS + JS + embedded images)
└── README.md
```

Inside `index.html`, in order:

1. `<head>`: meta tags, Open Graph tags, Google Fonts link, all CSS.
2. `<body>`: welcome sheet, header, hero, marquee, countdown, shop, story, values, size guide, FAQ, contact, footer, floating WhatsApp button, policy banner and dialog, bag drawer.
3. First `<script>`: configuration, product data, shop, bag, welcome animation, countdown, newsletter.
4. Second `<script>`: privacy and cookie policy logic.
5. Last `<script>`: embedded `LOGO` and `PHOTOS` (very large lines of base64 data).

## Getting started

No installation required.

1. Download or clone the project.
2. Open `index.html` in a browser.

That is the whole local setup. The newsletter form only works once deployed to Netlify. Locally it falls back to opening the visitor's email app.

## Configuration

Everything you are likely to change often is at the top of the first `<script>` block, under `// ===== EDIT THESE =====`:

| Constant | What it does |
| --- | --- |
| `NEWS_EMAIL` | Email used for the newsletter fallback (opens the visitor's email app if the form request fails). |
| `WHATSAPP` | WhatsApp number for orders and the chat buttons. Country code first, no `+` or spaces (for example `233XXXXXXXXX`). |
| `CURRENCY` | Currency label shown on prices (default `GH₵`). |
| `DROP_DATE` | Next drop, as an ISO date in UTC (Ghana time), for example `2026-12-12T18:00:00Z`. Use a future date or the countdown hides itself. |
| `BUNDLE_SAVING` | Amount saved when a jacket and a pair of socks are in the bag together. |

The WhatsApp number also appears in a few hard-coded links (header chat button, floating button, contact section). If you change the number, search the file for the old one and update every occurrence.

## Managing products

Products live in the `PRODUCTS` array in the first script.

```js
{
  id: "bomber",                 // unique, no spaces
  type: "jacket",               // tee | armless | jeans | jacket | socks
  photo: "bomber",              // key in the PHOTOS object
  name: "Unlisted Unmatched Bomber",
  price: 520,                   // number, in CURRENCY
  limited: 20,                  // number = "Limited run of N" tag, 0 = Sold out, remove for no tag
  blurb: "Black bomber with the slogan down the front and the logo on the chest.",
  colors: [{ n: "Black", h: "#1c1c1c", l: L_LIGHT }]
}
```

### Add a product

1. Add a new object to `PRODUCTS` with a unique `id`.
2. Add a matching photo to `PHOTOS` (see below), using the same key as `photo`.
3. Make sure `type` exists in both `LABELS` (filter buttons) and `SIZES` (size options).

### Add a new category

Add it to `LABELS` (for example `hoodie: "Hoodies"`) and to `SIZES` (for example `hoodie: ["S","M","L","XL"]`), then use it as a product `type`.

### Colours

`colors` takes one or more entries. With more than one, colour swatches appear on the card. `n` is the name, `h` the swatch colour and `l` the logo colour used for the legacy illustrated artwork.

### Photos

Photos are embedded as base64 JPEG data URIs in the `PHOTOS` object, so the site stays a single file.

- Recommended size: **828 × 1097 px** (3:4 portrait). Cards display photos at this ratio and crop to fit.
- Keep each image under roughly 100 KB where possible. The whole file is around 770 KB, mostly photos.
- To replace a photo, replace its data string. To add one, add a new `key: "data:image/jpeg;base64,..."` entry.
- Free tools such as base64.guru, or the command `base64 -w0 photo.jpg`, will convert an image to base64. Prefix the result with `data:image/jpeg;base64,`.

### Bundle logic

The bag counts jackets and socks. The saving is `min(jackets, socks) × BUNDLE_SAVING`, shown as a separate line and included in the WhatsApp message.

## Editing content

Text content is plain HTML. Look for the `<!-- EDIT: ... -->` comments:

- **Our story** and timeline (2025, 2026, Now, Next).
- **What we stand for** (three values).
- **Size guide:** measurements are flat, in cm. Measure your real pieces and update the tables.
- **FAQ:** answers should match how you actually work (ordering, payment, delivery, exchanges, limited drops).
- **Contact:** WhatsApp and X links.
- **Meta tags:** `<title>`, description and Open Graph tags in `<head>` control search and share previews.

The logo is the `LOGO` constant (white PNG with transparent background) in the last script block. It is used in the header, hero, welcome sheet and favicon.

## Newsletter setup (Netlify)

The signup form uses [Netlify Forms](https://docs.netlify.com/forms/setup/).

1. Deploy the site to Netlify. The `data-netlify="true"` attribute lets Netlify detect the form at deploy time.
2. In the Netlify dashboard, open **Forms**, then **Form notifications**, and add an email notification so new signups are sent to you.
3. Test with a real signup after deploying. Submissions appear under **Forms** in the dashboard.

A hidden honeypot field (`bot-field`) filters out most spam. If the request fails, the visitor is offered a pre-filled email to `NEWS_EMAIL` instead.

## Privacy and cookie policy

The site shows a policy banner after the welcome animation.

| Choice | Result |
| --- | --- |
| **Accept** | The bag is saved in the visitor's browser (`uyBag`) and is there next visit. |
| **Reject** | Nothing is saved. Any saved bag is deleted. |
| No answer yet | Nothing is saved until the visitor chooses. |

The choice itself is stored under `uyConsent`. Visitors can reopen the full policy and change their choice with the **Privacy & cookie policy** link in the footer.

The site uses no advertising or tracking cookies and no analytics. The policy text in the `<dialog id="policy">` element reflects this. **If you add analytics, ads or other third-party tools, update the policy text and the consent logic to match.** Have the policy reviewed against Ghana's data protection requirements before relying on it.

## Deployment

Any static host works (Netlify, Vercel, GitHub Pages, Cloudflare Pages). Netlify is recommended because the newsletter uses Netlify Forms.

**Netlify (drag and drop)**

1. Put `index.html` in a folder.
2. Go to the Netlify dashboard, then **Add new site**, then **Deploy manually**, and drop the folder.
3. Set up form notifications (see above).
4. Optional: connect a custom domain under **Domain management**.

**Netlify (Git)**

1. Push this repository to GitHub.
2. In Netlify, choose **Import from Git** and select the repository.
3. Leave the build command empty and set the publish directory to the repository root.

## Editing on mobile

This project is easy to maintain from a phone, for example in the Spck Editor.

- The photo and logo data are enormous single lines at the **very bottom** of the file. Avoid scrolling there, and edit everything above it.
- Use the editor's search to jump to `EDIT THESE`, `PRODUCTS`, `<!-- EDIT` or `Size guide`.
- Make one change at a time and reload the page to confirm it before moving on.
- Keep a backup copy before pasting new image data.

## Design system

| Token | Value | Use |
| --- | --- | --- |
| `--ink` | `#1a1a1a` | Text, header, dark sections |
| `--paper` | `#efede8` | Page background |
| `--cement` | `#dedbd5` | Image backgrounds, size guide |
| `--red` | `#d1382c` | Accent, marquee, promo |
| `--gold` | `#e3a72f` | Highlights, contact section |

**Type:** Big Shoulders Display (headings, uppercase, heavy), Instrument Serif italic (intros and notes), Work Sans (body). All tokens are CSS variables on `:root`, so a retheme is a few lines.

## Accessibility and performance

- Semantic landmarks, labelled buttons, visible focus rings and keyboard support (Escape closes the bag and the policy dialog).
- `prefers-reduced-motion` is respected: the welcome tear animation, marquee and hover zoom are disabled.
- Layout is responsive from small phones to wide desktops, and respects phone safe areas.
- Product images load lazily.
- Known trade-off: embedding all images keeps the site a single portable file, but the first load is about 770 KB. If the catalogue grows, consider moving photos to separate files in an `images/` folder and pointing `PHOTOS` at those paths.

## Browser support

Current versions of Chrome, Edge, Safari and Firefox on desktop and mobile. The policy pop-up uses the native `<dialog>` element. The flag emoji in "Made in Ghana 🇬🇭" shows as "GH" on Windows, which does not draw flag emojis.

## Pre-launch checklist

- [ ] `WHATSAPP` number is correct and every hard-coded WhatsApp link matches it
- [ ] `NEWS_EMAIL` is correct, and Netlify form notifications are enabled
- [ ] `DROP_DATE` is set to a future date
- [ ] Prices, `limited` counts and product descriptions are final
- [ ] Size guide measurements match the real garments
- [ ] Story and FAQ answers are accurate
- [ ] X link in the contact section points to the current brand account
- [ ] Privacy and cookie policy text reviewed
- [ ] Title, description and Open Graph tags are correct
- [ ] Test an order end to end on a real phone (add to bag, bundle saving, WhatsApp message)
- [ ] Test the newsletter signup on the deployed site

## License

© UNLISTED YOUTH STREETWEAR. All rights reserved.
