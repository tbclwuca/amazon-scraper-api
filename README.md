# ScraperAPI Amazon API: How to Scrape Amazon Product Data at Scale — What Are the Endpoints, What Does It Actually Cost, and Which Plan Is Right for You? (Complete Guide Including All Pricing Plans and Real Credit Math)

If you've ever tried to pull product data from Amazon — prices, reviews, BSR rankings, competitor offers — you already know what happens. You send a few requests, things look fine, and then Amazon's bot detection kicks in and you're staring at a wall of CAPTCHAs or a block page. Building and maintaining your own proxy rotation infrastructure to deal with that is a full-time job on its own.

That's the exact problem that **ScraperAPI** was built to solve. And for Amazon specifically, it's one of the more capable solutions on the market — offering dedicated structured data endpoints that return clean JSON instead of raw HTML you'd have to parse yourself.

But "capable" doesn't mean "simple to price out." ScraperAPI's credit system has a few traps that catch people off guard. Before you sign up and watch your credits disappear three days in, it's worth understanding exactly how the Amazon API works, what it costs at each tier, and whether it makes sense for your use case.

---

## What ScraperAPI's Amazon API Actually Does

ScraperAPI is a web scraping API that handles everything messy about large-scale scraping — proxy rotation across 40 million+ IPs in 50+ countries, automatic CAPTCHA solving, JavaScript rendering, and retry logic. You pass it a URL, it gives you back the page content. That's the core idea.

For Amazon specifically, ScraperAPI has gone further and built three dedicated **Structured Data Endpoints (SDEs)** that bypass the raw-HTML-and-parse-it-yourself workflow entirely:

- **Amazon Product API** — Pass an ASIN, get back structured JSON with product name, price, ratings, review count, images, BSR rank, variants (size/color), seller info, and all publicly available reviews for that product page. Supports 21 international Amazon marketplaces (amazon.com, amazon.co.uk, amazon.de, amazon.co.jp, and more).

- **Amazon Search API** — Pass a search keyword or phrase, get back structured JSON from an Amazon search results page — product listings, prices, ratings, sponsored results, all parsed and ready to use.

- **Amazon Offers API** — Get all competitor offers for a specific ASIN, including third-party seller pricing, shipping conditions, and offer conditions. Critical for Buy Box monitoring and repricing strategies.

Each of these also has an **async version**, which is useful when you're doing high-volume batch jobs and don't want to hold open HTTP connections waiting for synchronous responses.

The API call itself is simple. For a product lookup, it looks like this:

python
import requests

payload = {
    'api_key': 'YOUR_API_KEY',
    'asin': 'B07FTKQ97Q',
    'tld': 'com',           # Amazon marketplace (com, co.uk, de, etc.)
    'country_code': 'us'    # Geotargeting for shipping/pricing data
}

r = requests.get(
    'https://api.scraperapi.com/structured/amazon/product',
    params=payload
)

print(r.json())


You get back a JSON object with 18+ fields — price, review data, BSR, variant options, images, full product description, and more. No HTML parsing required.

For search results:

python
import requests

payload = {
    'api_key': 'YOUR_API_KEY',
    'query': 'wireless earbuds',
    'tld': 'com',
    'country_code': 'us'
}

r = requests.get(
    'https://api.scraperapi.com/structured/amazon/search',
    params=payload
)

print(r.json())


The `tld` parameter is how you switch between marketplaces. Scraping `amazon.de` from a US IP to get German pricing? Set `tld=de` and `country_code=de`. Both parameters need to be specified when you're crossing regional boundaries.

👉 [Explore ScraperAPI's Amazon API Features](https://www.scraperapi.com/solutions/ecommerce-data-collection/amazon-scraper/?fp_ref=coupons)

---

## The Credit System: This Is Where People Get Surprised

Here's the part most reviews skip, and it's the most important thing to understand before you start using ScraperAPI for Amazon data.

ScraperAPI bills on API credits. The surface-level premise is that 1 request = 1 credit. But that's almost never what actually happens, because different domains and different feature flags multiply the credit cost per request.

**The base domain multipliers:**

| Domain Type | Credits per Request | Examples |
|---|---|---|
| Normal website | 1 | Blogs, news sites, basic HTML |
| **E-Commerce (Amazon)** | **5** | Amazon, eBay, Walmart |
| SERP | 25 | Google, Bing |
| Social Media | 30 | LinkedIn |

So right off the bat: every Amazon request costs **5 credits**, not 1. That's before you add any parameters.

**Feature flag extra costs (stacked on top of domain multiplier):**

| Parameter | Extra Credits |
|---|---|
| `render=true` (JS rendering) | +10 |
| `premium=true` (premium proxies) | +10 |
| `screenshot=true` | +10 |
| `ultra_premium=true` | +30 |
| Anti-bot bypass (Cloudflare, DataDome, PerimeterX) | +10 each (auto-applied) |
| `premium=true` + `render=true` combined | +25 (NOT +20) |
| `ultra_premium=true` + `render=true` combined | +75 (NOT +40) |

Notice those last two lines. Combining features costs more than the sum of the individual parts. That's not a typo — ScraperAPI's combined feature pricing is non-linear, and it's documented but easy to miss.

**What this means in practice for Amazon:**

A basic Amazon product lookup (no rendering, no premium proxies): **5 credits**
Amazon + JavaScript rendering: **5 + 10 = 15 credits**
Amazon + premium proxy + JS rendering: **5 + 25 = 30 credits** (not 5 + 10 + 10 = 25)

The good news for the structured data endpoints: the SDE handles most of the complexity internally. You don't need to manually set `render=true` for the Amazon Product API — it takes care of the rendering on its end. Each SDE Amazon request costs 5 credits.

Parameters that cost zero extra: `wait_for_selector`, `country_code`, `session_number`, `device_type`, `output_format`, `keep_headers=true`, `autoparse=true`.

---

## The Plans: What You Actually Get at Each Tier

👉 [See All Plans and Start Free](https://www.scraperapi.com/pricing/?fp_ref=coupons)

ScraperAPI offers a free tier plus five paid tiers, with annual billing discounts of about 10%:

**Full Plan Comparison Table:**

| Plan | Monthly Price | Annual (per month) | API Credits/Month | Concurrent Threads | Geotargeting | Amazon Requests (5 credits each) |
|---|---|---|---|---|---|---|
| **Free** | $0 | — | 1,000 | 5 | No | ~200 |
| **Hobby** | $49 | $44.10 | 100,000 | 20 | US & EU only | ~20,000 |
| **Startup** | $149 | $134.10 | 1,000,000 | 50 | US & EU only | ~200,000 |
| **Business** | $299 | $269.10 | 3,000,000 | 100 | 50+ countries | ~600,000 |
| **Scaling** | $475 | $427.50 | 5,000,000 | 200 | 50+ countries | ~1,000,000 |
| **Enterprise** | Custom | Custom | 5,000,000+ | 200+ | 50+ countries | Custom |

**Purchase links by plan:**

| Plan | Link |
|---|---|
| Free (5,000 trial credits) |  [Start Free Trial](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby – $49/mo |  [Get Hobby Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup – $149/mo |  [Get Startup Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business – $299/mo |  [Get Business Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling – $475/mo |  [Get Scaling Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise – Custom |  [Contact Sales for Enterprise](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

A few things to know about these plans:

- **Credits do NOT roll over.** Unused credits expire at the end of each billing cycle.
- **Pay-As-You-Go is only available on Scaling ($475/mo) and above.** If you're on Hobby, Startup, or Business and burn through your credits mid-cycle, you're cut off until the next billing date. Your options are upgrade now or wait.
- **Geotargeting beyond US and EU requires the Business plan ($299/mo) or higher.** If you need to scrape amazon.de from a German IP, or amazon.co.jp from a Japanese IP, you can't do it on Hobby or Startup.
- **The 7-day trial** gives you 5,000 free credits to test at realistic scale before committing to a plan.
- **7-day no-questions-asked refund policy** — if you sign up and it doesn't work for your use case, they'll refund you.
- **Education plan** — ScraperAPI offers a separate academic/research plan for students and researchers. Worth applying if that fits your situation.

---

## Real Amazon Cost Math: What Each Plan Actually Gets You

Let's cut through the credit numbers and talk about actual Amazon product pages per dollar.

At a base cost of 5 credits per Amazon SDE request:

| Plan | Monthly Cost | Amazon SDE Requests | Cost per 1,000 Amazon Pages |
|---|---|---|---|
| Hobby | $49 | 20,000 | $2.45 |
| Startup | $149 | 200,000 | $0.75 |
| Business | $299 | 600,000 | $0.50 |
| Scaling | $475 | 1,000,000 | $0.48 |

The cost per page drops dramatically as you scale up. On the Hobby plan, you're paying $2.45 per 1,000 Amazon product pages. On the Business plan, that drops to $0.50 per 1,000. If your operation needs more than 20,000 Amazon lookups a month, upgrading from Hobby to Startup pays for itself fast.

For context: scraping 10,000 Amazon products per month on the Startup plan costs roughly $7.50 worth of your $149 plan. That's actually pretty reasonable for what you get — parsed JSON, no proxy management, no CAPTCHA headaches, 21 marketplace options.

---

## Performance: How Well Does It Actually Work on Amazon?

Independent benchmarks from Scrapeway (April 2026) put ScraperAPI's Amazon success rate at **98%** with an average response time of about 6.5 seconds. That's solid — Amazon is one of the harder sites to scrape consistently because of its aggressive bot detection, and a 98% success rate in production is genuinely good.

For context, ScraperAPI's overall average success rate across all sites sits around 62-64%, which is slightly above the industry average of 58-60%. The high Amazon number isn't a fluke — e-commerce sites are where ScraperAPI has invested the most heavily in their specialized scraping logic.

The data you get back from the Amazon Product API is comprehensive: product name, price, availability, ratings, review count, all publicly available reviews (for the product's ASIN), BSR rank, category breadcrumbs, variant options (size, color, style), images, full description, seller info. For most competitive intelligence and pricing monitoring use cases, that covers everything you'd need.

Supported marketplaces include: amazon.com, amazon.co.uk, amazon.ca, amazon.de, amazon.es, amazon.fr, amazon.it, amazon.co.jp, amazon.co.za, amazon.in, amazon.cn, amazon.com.sg, amazon.com.mx, amazon.ae, amazon.com.br, amazon.nl, amazon.com.au, amazon.com.tr, amazon.sa, amazon.se, amazon.pl — 21 regional domains in total.

---

## Who Actually Needs the ScraperAPI Amazon API?

The honest answer is: it depends on what problem you're actually trying to solve.

**ScraperAPI's Amazon API makes the most sense if you:**

- Have a developer or engineering team who can write Python, Node.js, PHP, Ruby, or Java
- Need to pull Amazon product data at scale — thousands to millions of ASINs per month
- Want clean structured JSON output without building and maintaining your own parser
- Are monitoring pricing, BSR, review sentiment, or Buy Box status across many products
- Need multi-marketplace support (checking the same product across amazon.com, amazon.de, amazon.co.jp simultaneously)
- Are building a data pipeline that feeds into another system (database, BI tool, ML model)

**It's probably not the right fit if you:**

- Need data from a site where ScraperAPI has low or zero success rates (Instagram, Twitter/X, Booking.com all show 0% in benchmarks)
- Are trying to access data behind a login wall — ScraperAPI explicitly forbids scraping protected/login-required pages
- Don't write code and just need a spreadsheet of Amazon product data
- Are doing a one-off data pull rather than ongoing monitoring

For non-technical users or small one-off jobs, ScraperAPI is genuinely the wrong tool. It's an API built for developers building systems. If that's not you, a browser-based no-code scraper will get you to a spreadsheet in five minutes without credit multipliers.

---

## Three Specific Use Cases Where ScraperAPI's Amazon API Delivers

**1. Competitive pricing monitoring**

If you're an Amazon seller tracking competitor prices across hundreds of ASINs, the Amazon Offers API is exactly what you need. You can pull all third-party offers for any ASIN — seller names, prices, conditions, shipping details — and feed that into a repricing system. At $0.75–$2.45 per 1,000 requests depending on your plan, running hourly checks across 500 ASINs is financially realistic.

**2. Market research and product discovery**

The Amazon Search API lets you pull structured results for any keyword search. Feed in "wireless earbuds" or "ergonomic chair under $200" and get back product listings, prices, ratings, and sponsored placements as structured JSON. For market research firms building category-level trend reports, this is significantly faster than any manual process.

**3. Review sentiment analysis**

The Amazon Product API returns all publicly available reviews for a product. Feed ASINs for your products and competitors' products into a pipeline, collect the review text, and run it through a sentiment model. ScraperAPI handles the Amazon scraping; your team handles the analysis. The separation of concerns is clean, and scaling up the data collection doesn't require infrastructure work — just a higher plan tier.

---

## What Real Users Say

ScraperAPI has a 4.4/5 on G2, 4.6/5 on Capterra, and 4.5/5 on Trustpilot. The pattern in reviews is pretty consistent: people love how easy it is to set up (Capterra Ease of Use: 4.9/5), and most complaints cluster around the credit pricing complexity — specifically around multipliers being confusing and credits expiring without rollover.

One Capterra reviewer described the credit cost breakdown as "confusing." A Reddit user reported being quoted a price for 60 million credits expecting 1 credit per Amazon request, and then discovering after payment that the 5x multiplier applied — effectively getting 12 million Amazon requests instead of 60 million. ScraperAPI does document the multipliers, but you have to go looking for it in the docs rather than seeing it upfront on the pricing page.

The practical advice: use ScraperAPI's built-in **Domain Multiplier tool** in the dashboard before committing to a plan. Plug in the URLs you plan to scrape, and it tells you the actual credit cost per request. Don't trust the headline credit numbers until you've run your specific use case through that tool.

---

## Quick Setup: Getting Started in Under 10 Minutes

👉 [Sign Up Free — 5,000 Trial Credits, No Credit Card Required](https://www.scraperapi.com/?fp_ref=coupons)

1. Sign up for a free account. You get 1,000 permanent monthly credits plus 5,000 trial credits for the first 7 days.
2. Grab your API key from the dashboard.
3. Use the API Playground to test your first Amazon request — plug in an ASIN and see the JSON response before writing any code.
4. Check the Domain Multiplier tool for any URLs you plan to scrape regularly.
5. Once you've validated your use case, pick the plan tier that matches your monthly volume.

The documentation is genuinely good — there are code examples in Python, Node.js, PHP, Ruby, and Java for every endpoint. Getting from zero to a working Amazon data pull is realistically a one-afternoon project for most developers.

---

## The Bottom Line

ScraperAPI's Amazon API is a solid, production-ready tool for developers who need reliable structured Amazon data at scale. The 98% success rate, 21-marketplace support, and clean JSON output with 18+ fields make it one of the more complete options in this space. The credit system is the main thing to understand upfront — Amazon requests cost 5 credits each, not 1, and combining features like premium proxies and JS rendering can stack costs further.

If you're building a data pipeline, running competitive price monitoring, or doing market research across multiple Amazon marketplaces, it's worth at least running through the 5,000-credit free trial to see how it performs on your specific targets.

If you need something simpler or don't write code, that's fine too — just know going in that ScraperAPI is built for engineering teams, and there are better tools for non-technical use cases.

For everyone else: start with the free trial, use the Domain Multiplier tool to reality-check your projected monthly costs, and pick the plan that fits your actual volume.

👉 [Start Your Free Trial on ScraperAPI](https://www.scraperapi.com/?fp_ref=coupons)
