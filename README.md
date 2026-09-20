# CEVYQ Prospector — Build Log

**Status:** Built, hardened, and sale-ready — pre-launch | **Type:** Commercial desktop product | **Built by:** CEVYQ Group, Business & Technology Solutions

## What this is

CEVYQ Prospector is a Windows desktop application that helps salespeople, marketing agencies, and B2B outreach professionals actually understand a prospect before they pitch them — not just find a list of business names. It searches for local businesses, enriches each result with verified contact and social data, and exports a clean, ready-to-use Excel sheet. It's sold as a one-time purchase rather than a subscription.

This log documents how it was built and hardened to the point of being genuinely sale-ready. I'm not a software developer — I don't write code. Every part of this was built by pressure-testing the business problem, understanding what a salesperson actually needs before a pitch, and using AI-assisted tools to turn that into working, secure software. The engineering decisions below were driven by product and business judgment first; the implementation followed from that.

## Why this exists

Most tools in this space stop at "find a list of businesses." That's not enough to pitch well — you need to know enough about a prospect to have a real conversation with them, not a cold generic one. CEVYQ Prospector was built to close that gap: find the business, then enrich it with the contact and social details that actually help someone prepare a pitch, and hand it over in a format (Excel) people already know how to work with.

## The build, in order

### 1. Core enrichment engine
Built the core product around real business enrichment, not just business discovery. The tool searches for local businesses and then pulls verified contact and social details for each one, so the output is something a salesperson can actually use to prepare — who to contact, how to reach them, what they're about — rather than a bare list of names and addresses. This is the difference between a scraper and a prospecting tool, and it's the core value the whole product is built around.

### 2. Licensing and security system
Because this is a paid, one-time-purchase product distributed to customers as an installable app, it needed real protection against casual license sharing and tampering — not just a login screen. Built a server-backed license validation system where the app checks a live, time-limited authorization token at multiple separate points during each search, rather than trusting a single check at startup that could be bypassed. Tokens are short-lived and refreshed automatically in the background, with retry handling for normal network hiccups so a legitimate customer's session doesn't break because of a brief connection issue.

### 3. Dual-build architecture: resale vs. internal
Split the product into two separate builds from the same codebase: the customer-facing resale build, which enforces daily and monthly usage caps to control operating costs, and a separate internal build for admin/testing use with no cap and no installer. Both builds share the same usage tracking under the hood, deliberately, so real combined API usage across both is always counted accurately — undercounting usage would mean under-provisioning for real cost, which is a business risk, not just a technical one. The product itself is compiled down to a native Windows executable (via Nuitka) rather than shipped as raw scripts, packaged with a proper installer, so a non-technical customer gets a normal, professional install experience.

### 4. Usability features built from actual usage patterns
Added a set of features driven by watching how the tool would actually get used day-to-day rather than how it "should" work in theory: a search history so previous results aren't lost between sessions, a cancel button that still exports whatever results were already found instead of throwing away a run that was going fine, and daily/monthly usage meters so a customer always knows where they stand against their limits before they run out mid-search.

### 5. Security audit
Before treating the product as sale-ready, ran a full audit specifically looking for ways the license system or backend could fail unsafely. Found and fixed a real exposure: an unhandled exception path in the backend validation service that could have surfaced more internal information than intended if it was ever hit. This is the kind of issue that's invisible in normal use and only shows up under adversarial testing — catching it before launch rather than after was the point of doing the audit at all.

### 6. Full rebrand across the live product
Rebranded the product twice over its build history, ending on CEVYQ Group. The final rebrand pass wasn't just swapping a logo — it meant going through the actual running application, the build scripts, and the installer to replace every old-brand reference. In the process, caught a real customer-facing issue: an activity log inside the app was still printing an old internal brand name to the screen during normal use, which would have shown a paying customer a name that didn't match the product they bought. Fixed everywhere it appeared, verified with a full diff against the previous version so nothing else was accidentally changed in the process.

## Where it stands

The final build is complete, hardened, and ready to sell. What's above is the actual path it took to get there — not a straight line from idea to finished product, but a real build with real security issues found and fixed before they could reach a paying customer.

---

*Source code, license validation internals, and backend infrastructure details are intentionally not included in this repository — CEVYQ Prospector is a commercial product. This log documents the build process and problem-solving behind it, not its implementation.*
