# FileYourStateTaxes

Static rebuild of [www.fileyourstatetaxes.org](https://www.fileyourstatetaxes.org),
Code for America's free state filing service for taxpayers using IRS Direct
File, in English and Spanish. The service closed on October 31, 2025; the
site now explains that, links former filers to their returns and refunds, and
keeps the state FAQs, privacy policy, and SMS terms published.

A fork of [CfA Static](https://github.com/codeforamerica/cfa-static), branched
from its tip at `9780b96d` with the template's full history; template updates
arrive only through a reviewed `upstream` merge.

## Status

Mirrors every public page of the live site as captured on 2026-10-05 — 107
pages:

| Route | Pages |
| --- | --- |
| `/en/`, `/es/` | Home: the closure notice, the summary, and the two buttons |
| `/{en,es}/us/faq/` | FAQ hub: every state's questions |
| `/{en,es}/{az,id,md,nc,nj,ny}/faq/` | One state's questions (live, though nothing links them) |
| `/{en,es}/{state}/faq/{question}/` | 43 answers per language (8 for Arizona, 7 for each other state) |
| `/{en,es}/privacy-policy/`, `/{en,es}/sms-terms/` | The privacy policy and text-messaging terms |
| `/404.html` | The live site's not-found page |

The live site hosts no documents of its own: the PDFs its answers mention
(state request forms) are linked on the state agencies' sites. Each language
was converted from its own scraped page, so their copy, links and quirks
differ where the live pages do.

Scraped ground truth (raw HTML, computed styles, screenshots at 1280 and
390 pixels, assets, and capture notes) lives in the gitignored
`.mirror-scratch/` directory; `.mirror-scratch/NOTES.md` records the routes,
redirects, palette, and source quirks.

## Customisations from template defaults

- **Collections and CMS:** pages and snippets only, with permalinks,
  redirects, no-index and navigation URLs editable; set with
  `npm run customise-cms` and saved as `cms_config` in `src/_data/site.json`.
  The news and guide collections, demo pages, images and snippets are gone,
  and with them `/blocks/`, `/search/` and `/theme-editor/`.
- **Languages:** `en` (default, under `/en/`) and `es` in
  `src/_data/languages.json`; all 53 page pairs in
  `src/_data/translations.json`, so every page's language links go to the
  same page in the other language.
- **Pages:** `src/pages/{en,es}/`. Each answer page is two `markdown` blocks —
  the question, then the answer — and the theme draws the answer as the live
  white card. Keep that shape when adding a question. The hub renders each
  state as an `h2` under one visually hidden `h1` (the live hub uses six
  `h1`s). Link-only menu entries (`src/pages/en/espanol.md`,
  `src/pages/es/english.md`) give the phone menu its language row.
- **Header and footer:** `logo` in `site.json`; `collapse_menu: mobile`;
  footer copy in `src/snippets/footer-content.md` and
  `src/snippets/es/footer-content.md`. The live site shows the language link
  in the header and at the end of the footer list, but `language_switcher`
  allows one placement, so it is `footer` and
  `src/_includes/navigation-end.html` (the template's header slot) renders
  the same switcher in the header. That slot override is the only template
  file this site changes.
- **Redirects** (`redirect_from` on the target pages): `/` → `/en/`; the
  unprefixed `/us/faq/`, `/privacy-policy/` and `/sms-terms/`; and the live
  302s from `/{en,es}/coming-soon/` and `/{en,es}/{state}/landing-page/` to
  each language's home page.
- **Theme:** `src/css/theme.scss` carries the live palette (charcoal
  `#3C3A3A` chrome, cream `#FBFCF7` page, teal `#007C7C` links, amber notice),
  the variable Inter and Geologica fonts the live site serves
  (`src/assets/fonts/`, SIL Open Font License 1.1), the 51px sticky header and
  full-screen phone menu, the live breakpoints (481px and 601px), the answer
  card, the three-quarter-width "slab" layout of the legal pages and 404, the
  launch mark on external links, and the 960px footer grid. Each state's FAQ
  pages wear that state's color — Idaho `#30462F`, Maryland `#2A3760`, North
  Carolina `#092940`, New Jersey `#112F4E`, New York `#184A72` — keyed on the
  page class the template derives from the URL; New Jersey's answers sit
  unboxed with unbulleted lists, as on the live site. Icons and the
  favicon are in `src/assets/fyst/` and `src/assets/favicon/`.
- **Config toggles:** breadcrumbs, theme switcher, placeholder images, URL
  linkification and new-tab external links off, as on the live site.

### Deliberate differences from the live site

- The Rails app's server-side flows (sign-in, archived returns, verification
  codes) cannot be static pages. The home page's "Download your return" goes
  to `pya.fileyourstatetaxes.org` as on the live site, and the Arizona and New
  York answers keep their absolute links to the live sign-in page, which will
  break if the app is retired.
- The phone menu's language row links to the other language's home page; the
  header and footer links go to the same page in the other language, as on
  the live site.
- Self-links are site-relative, so the privacy policy's
  "FileYourStateTaxes.org" and the SMS terms' privacy-policy URL lose the
  launch mark the live absolute URLs carry, and open in the same tab.
- The privacy policy's TRUSTe seal is a text link; the live seal image is
  broken and loads from a third party.
- The template obfuscates email links until its script runs, and the live
  site's analytics, chat widget and session cookies are not carried over.
- Redirects are static redirect pages rather than HTTP 302s.

### Source quirks kept verbatim

The home and hub pages share the doubled title "Free state tax filing |
FileYourStateTaxes | FileYourStateTaxes"; Arizona lists two refund-status
questions; Spanish state headings end in a colon; some Spanish answers differ
from the English (New Jersey's Form DCC-1 is unlinked, "Haz clic aquí" goes
to the English GetYourRefund page); and the not-found page's title names
GetYourRefund and mentions a "Help"/"Chat" button the site no longer has.

## Working on it

- [CLAUDE.md](CLAUDE.md) and the
  [Site Builder Reference](docs/developer-reference.md)
- The generated block reference,
  [skills/cfa-static-site-builder/references/blocks.md](skills/cfa-static-site-builder/references/blocks.md),
  is the authority on block fields (the `/blocks/` gallery page was removed
  with the demo pages); regenerate references with
  `npm run generate-references`
- Site data in `src/_data/`, pages in `src/pages/`, theme in
  `src/css/theme.scss`

## Checks

```sh
npm run test         # every check below plus the template's test suite
npm run build        # Eleventy build + Pagefind + internal link check
npm run check:a11y   # axe WCAG 2.2 AA audit of every built page
npm run lint:scss    # stylelint, after theme changes
```

Node 22 (see `package.json` `engines`).

## Deployment

Not configured yet. The template's SharedServices and GitHub Pages workflows
are present under `.github/workflows/`, and `app.yaml` still carries the
template's registration; see the [CfA Static deployment
docs](https://github.com/codeforamerica/cfa-static#deployment) before enabling
either. `site.json` sets the public URL to
`https://www.fileyourstatetaxes.org`; a deployment elsewhere overrides it with
`SITE_URL`.
