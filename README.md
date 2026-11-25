# Business directory scraper: what a city sweep costs in credits, which plan covers it, and when a ready-made directory API wins (full plan comparison)

Three things break directory scraping projects: the listing pages sit behind Cloudflare or Datadome, pagination renders client-side, and every directory names its fields differently. ScraperAPI prices the fetch rather than the record. A standard page costs 1 credit, a page that needs bot-protection bypass adds 10, and the free plan hands you 1,000 credits with a maximum of 5 concurrent connections. That structure lets you cost a job before you pay for one, which is what most of this article is about.

> Most readers pulling a few thousand directory pages a month should price their target in credits first, then buy the smallest tier that covers it with headroom. If your volume is spiky, pick a plan with pay-as-you-go overflow: Hobby, Startup and Business turn requests away with a 429 once credits run dry, while Scaling and above let you keep going at a fixed per-credit rate up to a cap you set. 👉 [See the current plan lineup and per-plan prices](https://www.scraperapi.com/pricing?fp_ref=coupons)

## All ScraperAPI plans, and the one difference that decides your choice

ScraperAPI prices each tier by credit allowance, and upgrading usually lands you a better price per credit. Those figures move, so take the live numbers from the pricing page. The mechanics in the table below change far less often, and they decide whether a bad month stops your pipeline dead.

| Plan | Billing | When credits run out | Pay-as-you-go past the cap | Get it |
| --- | --- | --- | --- | --- |
| Free | Monthly | Requests stop until credits renew | No | [Start on the free plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | Monthly or annual | Opt-in auto-upgrade to the next tier | No | [See Hobby pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | Monthly or annual | Opt-in auto-upgrade | No | [See Startup pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | Monthly or annual | Opt-in auto-upgrade | No | [See Business pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | Monthly or annual | Opt-in auto-upgrade | Yes, fixed per-credit rate up to a monthly cap | [See Scaling pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | Monthly or annual | Opt-in auto-upgrade | Yes, same fixed-rate model | [See Professional pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | Monthly or annual | Pay-as-you-go takes over | Yes | [See Advanced pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | Monthly or annual | Pay-as-you-go takes over | Yes | [See Enterprise pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |

The free plan is the only allowance ScraperAPI states outright in its pricing FAQ: 1,000 API credits and 5 concurrent connections, with extra test credits available by asking support. Everything above it scales the credit pool and the concurrency ceiling together.

Two behaviours are worth memorising before you choose. Going over your concurrency limit returns a 429 that asks you to dial back and retry; there is no surcharge and the request is refused rather than queued. And credits are a monthly allowance that refreshes at renewal. Annual accounts can renew early to pull the next batch forward, which matters if you burn through the pool in week two of a big project.

## What a single request actually costs

One credit buys a standard page. Everything else is a multiplier, and the multipliers are where directory budgets go wrong.

| Target | Credits per request |
| --- | --- |
| Standard page | 1 |
| Amazon | 5 |
| Google or Bing, including subdomains | 25 |
| LinkedIn | 30 |
| Page behind Cloudflare, Datadome or PerimeterX, with bypass | +10 on top of the base cost |
| Sites with custom handling | Priced per site; the Domain Cost Estimator in your dashboard shows the exact figure |

You can cap the damage per call with the `max_cost` parameter, which keeps any single request inside a limit you set. Run the Domain Cost Estimator against three sample URLs from your target directory before you commit to anything. If those three come back at 11 credits instead of 1, your whole budget changes.

## Converting a directory into a credit number

Take a typical metro sweep: 40 category-and-city listing pages plus 2,000 business detail pages. On a directory that answers plainly, that job costs 2,040 credits. On a directory that throws a Cloudflare challenge at every request, each of those 2,040 calls carries the +10 bypass, so the same 2,000 records cost 22,440 credits. Identical output, eleven times the bill.

Recurring work compounds the same way. Say you refresh ratings, review counts and claimed status weekly for 2,000 businesses: 8,000 detail pages plus roughly 160 list pages, so about 8,160 credits a month on an open directory, and about 89,760 if the site fights back. That is the number to hold against any per-record alternative.

For scale, Dun & Bradstreet data through an Apify actor runs $1.49 per 1,000 companies for search-only rows and $6.48 per 1,000 with full company profiles on its free tier, falling to $0.89 and $4.38 at the Gold tier. Those are per-record prices, so they include the parsing and the schema. ScraperAPI's credit figures above include neither. Multiplying 2,040 credits by your plan's per-credit rate tells you which model is cheaper for your job; guessing at it usually tells you wrong. 👉 [Check what a credit costs on each plan](https://www.scraperapi.com/pricing?fp_ref=coupons)

## How far 1,000 free credits get you

Free tiers in this category look similar and behave nothing alike. Three shapes dominate:

| Tool | Free allowance | How the limit behaves | What it finishes |
| --- | --- | --- | --- |
| ScraperAPI | 1,000 credits, 5 concurrent connections | Fixed trial balance; support can add test credits | About 1,000 standard pages, or roughly 90 pages on a protected site |
| Bright Data directory scraper | 5,000 credits per month, about $7.50 of value as of its February 2025 page | Renews on the 1st, unused credits don't carry over, no card required | A monthly pool, so the cap resets whether or not you used it |
| Apify Manta scraper | 10 businesses per run for free users | Per-run cap, not a monthly pool; paid users can request up to 1,000,000 | 10 records, which won't cover a single city category |

The practical question is whether you can finish one city category before paying. A mid-size metro restaurant category usually holds 300 to 800 listings, so 1,000 credits covers the detail pages plus the listing pages on an unprotected directory with room to spare. On a protected one you get about 90 pages, enough to confirm the schema and not much else.

## Fields: what arrives, and what you build

Directory listings across the sites people actually target carry a fairly consistent field set: business name, category or industry, street address, city, state, phone, website, opening hours, services, rating, review count, and a claimed or verified flag. Description and logo show up often enough to plan for.

ScraperAPI returns the response body. Every field above is there if the page displays it, and none of them arrive pre-labelled. You write the selectors. ScraperAPI's own Google Maps walkthrough follows exactly that split: the API handles the fetching, then Selenium scrolls the results panel and BeautifulSoup pulls the fields into a CSV. Budget for the parsing work, because it is the part that breaks when a directory redesigns its template.

Export formats are yours to choose for the same reason. JSON, CSV and Excel are a few lines of code away, and so is a direct write into a CRM. Nothing is handed to you in a fixed schema.

## Build it yourself, and the defaults that set your speed

The open-source route is more viable than it looks. A generic Python directory scraper on GitHub keeps every site-specific setting in `config.yaml`, uses Playwright for JavaScript-rendered listing pages and requests with BeautifulSoup for detail pages, and ships with resume support, normalised phone and email fields, and a 0–100% data-completeness score that highlights weak rows in Excel.

Its defaults also show the real throughput ceiling. `http_threads` is 1, meaning one detail-page request at a time. Random delays run 0.15 to 0.45 seconds between requests. `max_retries` is 6, `request_timeout` is 25 seconds, `playwright_wait_ms` is 800, and `max_listing_pages` caps pagination at 500. At one to two seconds per page, 2,000 detail pages take somewhere between 40 minutes and over an hour, before retries and failures. Push the thread count and you own the proxy pool, the block rate and the politeness question yourself.

That is the honest trade. Self-hosted means you write the parser, source the proxies, handle retries and rebuild selectors when a directory changes its markup. A managed fetch keeps proxy rotation, unlocking and concurrency inside the per-request price, and you still write the parser. 👉 [Compare what each ScraperAPI plan includes](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Where ScraperAPI is the wrong purchase

Three situations call for something else. If you need a few dozen records once, hand-collection or a free actor run is faster than setting up an account and a parser. If you need DUNS numbers, revenue figures or key principals, a directory data API returns those fields directly; ScraperAPI can only return what the public page shows, and D&B masks financial detail behind its own paywall anyway. If you want a pre-mapped Manta schema with 18 named fields out of the box, a purpose-built actor already exists and saves you the selector work.

The other constraint is overflow. Hobby, Startup and Business have no pay-as-you-go option, so a heavier-than-expected month ends in 429s until renewal unless you enable auto-upgrade or move up a tier yourself. Teams with uneven monthly volume should start at Scaling or above.

## Questions that come up before buying

**Will 1,000 free credits finish one city?** One category in one city, usually yes, provided the pages cost 1 credit each. Check the Domain Cost Estimator first. At 11 credits a page you get about 90 pages.

**Can it scrape Yellow Pages, Manta or BBB?** ScraperAPI works at the request level and doesn't ship per-site parsers, so the site's defences decide the cost, not its name. Estimate a URL from each target directory and the answer shows up as a number.

**What happens when I hit my limit?** You get a 429 and the request is turned away, with no extra charge. On Hobby through Professional, opt-in auto-upgrade moves you to the next tier. On Scaling and above, pay-as-you-go keeps requests flowing at a fixed per-credit rate up to your monthly cap.

**Can I cap spending on a single runaway request?** Yes, `max_cost` sets a per-scrape ceiling.

**Monthly or annual?** Annual accounts can renew early for a fresh batch of credits, which helps when a project front-loads its volume. The per-credit rate also improves as you move up the tiers, so a bigger annual plan sometimes costs less per page than a smaller monthly one.

## Picking your tier

Match the plan to the job, not to the label. Testing the waters on one category in one city: stay on the free 1,000 credits and spend them on the Domain Cost Estimator before spending them on pages. Running a few thousand pages a month with steady volume: Hobby or Startup, sized to your credit count with 20–30% headroom for retries and failed pages. Refreshing thousands of records weekly, or scraping directories that fight back at 11 credits a page: Scaling or above, because the pay-as-you-go overflow is what keeps a heavy month from ending in 429s.

Two checks before you pay. Run three real URLs from your target directory through the Domain Cost Estimator and write down the credit cost per page. Then count your list pages and detail pages, multiply, and add the retry margin. Those two numbers tell you which tier you need faster than any feature list will.

👉 [Start with the free 1,000 credits or pick a paid plan](https://www.scraperapi.com/pricing?fp_ref=coupons)
