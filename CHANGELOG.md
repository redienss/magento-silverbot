# Changelog

SilverBot has no tagged releases or version numbers, so this log groups changes by the
calendar week they landed on `main` (Monday–Sunday, ISO week), newest first. Weeks with no
commits are omitted.

It is a hand-curated summary of user- and operator-visible changes across both the public
infrastructure repo and the private application repo. Routine refactors, documentation and
tooling commits are collapsed or left out.

Deploys to the public demo (`demo.silverbot.pl`) were not tracked before September 2026; where
a week's work reached the demo that week, it is noted.

## 2026-09-07 – 2026-09-13

Product page and Polish storefront polish. Rolled out to the demo on 2026-09-07.

- Rebuilt the product page as two independent column stacks so a long description no longer
  pushes the price chart and composition panel far down the page; on desktop the
  "More Information" panel automatically reflows to the shorter column.
- "Silver Purity" now shows as `925` and "Silver Weight" as a plain rounded gram value in the
  More Information panel, instead of the raw stored decimals.
- Composition panel: the spec table gained proper cell spacing and rules (the Fraction and
  Percentage columns no longer run together); the pie chart moved below the table at 75% width
  on desktop.
- Product stock status reworded as marketplace offer availability — "Current offer" /
  "Offer no longer available" (Polish: "Oferta aktualna" / "Oferta niedostępna") — since
  listings point to OLX rather than being sellable stock.
- Polish storefront: the footer link columns and the store-view name are translated to Polish,
  and the layered-navigation "Shop By" heading now reads "Filtry".
- Public README and screenshots refreshed for the Hyvä storefront.

## 2026-08-31 – 2026-09-06

Storefront migrated to the Hyvä theme, a Polish/English storefront, and automatic pruning of
dead offers. Rolled out to the demo on 2026-09-05 (Hyvä theme and composition backfill) and
2026-09-06 (marketplace parity, availability sweep, Polish storefront).

- **Hyvä theme.** The storefront moved from the Luma-based theme to a Hyvä child theme
  (`Redienss/hyva-silvertheme`). The logo, Open Sans font, "Go to Offer" grid, per-unit silver
  price metrics, SPOT premium badge, saved-filters sidebar, header spot-price bar and
  range-filter controls were all ported to work under Hyvä. New SilverBot dark colour scheme
  (cyan / gold, derived from OLX), with OLX cyan reserved for "Go to Offer" actions.
- **Polish storefront.** Added an English store view at `/en/` with a header PL/EN switcher and
  a footer language selector; Polish is the default. Built a consolidated `pl_PL` dictionary
  covering custom strings and core gaps, wrapped the spot bar, chart, composition panel and
  alert e-mail for translation, and added per-store-view product attribute labels. Product and
  category content stays Polish on both views.
- **Marketplace-redirect parity.** "Go to Offer" replaces Add to Cart on the wishlist and
  compare pages; the header cart icon, mini-cart drawer and cart-page checkout button are
  hidden; the product-tile hover border is kept; homepage and product-page breadcrumbs are
  trimmed; the newsletter button reads "Subscribe".
- **Offer-availability sweep.** A new cron job re-checks imported offers against their OLX page
  and removes the product (with its images) after repeated 404/410 responses; rate-limit
  responses are treated as inconclusive and never delete anything.
- **Composition fix.** Elemental composition percentages are normalised to sum to 100% at
  ingestion, and existing data was backfilled.
- The homepage shows the Silver category grid with layered navigation, resolved dynamically
  rather than by a hard-coded ID.
- The price chart hides the product-price reference line when the asking price sits far above
  the silver spot all-time high, so the history stays readable.
- The import queue worker was throttled to run every 15 minutes.
- Added an internal knowledge base and a branch-per-change pull-request workflow.

## 2026-07-06 – 2026-07-12

Module split, CLI cleanup and a README rewrite.

- Split the single module into four: `Redienss_SilverBot` (core), `Redienss_SilverBotOlx`
  (OLX scraping), `Redienss_SilverBotAlphaVantage` and `Redienss_SilverBotGoldAPI`
  (spot-price providers).
- Consolidated the CLI: removed the ad-hoc `import-newest` / `import-next` / `test-offer` /
  `test-newest` commands, renamed the rest to a consistent `silverbot:test-openai` /
  `test-olx` / `test-goldapi` / `import-xagpln-csv` scheme, and shared the offer-import path
  between the cron worker and `import-by-id`.
- Removed the obsolete daily "report" e-mail (superseded by saved-filter alerts).
- Compacted the header spot-price stats; added a Test Connection button to the AlphaVantage
  admin config.
- Rewrote the README for the multi-module architecture.

## 2026-06-29 – 2026-07-05

Saved filters and e-mail alerts.

- Logged-in customers can save layered-navigation filter sets and toggle an e-mail alert per
  set; when a newly imported product matches a saved filter, an alert e-mail with the silver
  metrics is sent. Added `mageplaza/module-smtp` for outbound mail.
- "Go to Offer" replaces Add to Cart on category, search and CMS-widget product tiles.
- "Silver Weight (g)" became a min / max range filter.
- Price chart: taller on desktop and mobile, smoother line, and a single legend toggle for the
  regression channel.
- Each imported product gets a unique URL key to avoid slug collisions.

## 2026-06-22 – 2026-06-28

A spot-price provider, a price-history chart and elemental composition.

- Added the `Redienss_SilverBotAlphaVantage` module and switched the spot-price cron to
  AlphaVantage.
- The header shows XAG/USD, XAG/PLN, USD/PLN and the imported-product count.
- New Price Chart on the product page (Chart.js): one year of daily XAG/PLN closes with
  SMA 9 / SMA 21, a linear-regression channel, a reference line at the product's own per-oz
  price coloured by deal quality, and 5D / 1M / 3M / 6M / YTD / 1Y ranges. History is imported
  from stooq.pl via `silverbot:import-xagpln-csv`.
- OpenAI now estimates elemental composition and is sent the first offer photo for multimodal
  analysis; composition is shown as a table plus a colour-coded pie chart with an AI-estimate
  disclaimer, and rendered readably in More Information.
- Layered navigation: the predefined price ranges were replaced with a min / max input form,
  a Silver Purity filter was added, and min / max filtering was fixed for OpenSearch.
- Removed the Reviews tab.

## 2026-06-15 – 2026-06-21

- Created the public infrastructure repository: the Docker Compose stack, `bin/` wrappers,
  environment templates, the project README and the first screenshots.
- Added a Silver category and assigned imported products to it; moved the logo into the theme.
- Documentation pass across the module; removed leftover scaffolding modules.

## 2026-06-08 – 2026-06-14

An import queue and the silver spot price.

- Replaced the one-offer-at-a-time cron with a persistent import queue: a populate job
  paginates the full OLX listing into the queue and a worker job processes it.
- Added spot-price storage and a cron that fetches the XAG/PLN spot price; the header shows the
  current spot and the product page shows a "SPOT ±X%" premium / discount versus the product's
  per-oz price.
- Added min / max range filters for the per-gram, per-oz and per-kg silver prices, and a sort
  order for the More Information attributes.

## 2026-06-01 – 2026-06-07

The initial import pipeline.

- First working version of `Redienss_SilverBot`: scrape a silver listing from OLX, send it
  (with photos) to OpenAI to estimate purity, pure-silver weight and per-unit prices, and
  create a Magento product with eight custom attributes (`olx_id`, `olx_url`, `ag_purity`,
  `ag_weight`, `ag_price_1g`, `ag_price_1oz`, `ag_price_1000g`).
- Admin configuration for the OpenAI API (with a Test Connection button and an editable
  prompt), a report e-mail address, the OLX base and query URLs, and an "Enable Cron" master
  switch.
- CLI commands to test the OpenAI connection and to import a listing by ID, newest or next.
- Storefront: per-unit silver prices under the price on product tiles; a "Go to Offer" link to
  the OLX offer instead of Add to Cart on the product page; store locale set to Polish /
  Europe-Warsaw / PLN.
- The OpenAI prompt was translated to English and purity fractions normalised to a 1–999
  scale.

## 2026-05-25 – 2026-05-31

- Repository created.
