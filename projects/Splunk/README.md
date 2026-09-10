# Splunk Investigation — Checking 404s and 403s for Anything Suspicious

This is a writeup of a small investigation I did using Splunk's tutorial dataset (sample e-commerce site traffic) to practice how a SOC analyst would triage errors in web logs — basically, figuring out if failed/denied requests mean something bad is happening, or if it's just normal traffic.

## What I was trying to find out
Whenever a server logs a lot of 404s or 403s, it *could* mean someone is scanning the site looking for hidden pages, or trying to access something they're not allowed to. I wanted to check if that was actually happening here, or if it was just normal noise.

## Setup
- Splunk Enterprise (free license), running locally
- Used the sample dataset from Splunk's own Search Tutorial (simulated web traffic for an online store)

## Part 1 — Looking at 404 errors (page not found)

**Why I checked this:** if someone's probing a site for weak spots, they'll often request a bunch of pages that don't exist (like `/admin`, `/config.php`, etc.) to see what sticks. So a lot of 404s from one source is a red flag.

**Query I ran:**
```spl
index=main sourcetype="access_combined_wcookie" status=404 | stats count by clientip | sort -count
```

**What I got:** 690 total 404s, but spread out across 174 different IPs. The top IP only had 20 of them — and that same IP had made 1036 requests overall, so barely 2% of its traffic was even a 404.

**What I concluded:** nothing suspicious here. If someone was actually scanning the site, I'd expect one or two IPs to account for a huge chunk of the 404s, not a spread this even. This just looks like normal users occasionally clicking broken/old links.

## Part 2 — Looking at 403 errors (forbidden/access denied)

**Why I checked this:** 403s can mean someone's repeatedly trying to get into something they don't have permission for.

**Query I ran:**
```spl
index=main sourcetype="access_combined_wcookie" status=403 | stats count by clientip, uri_path | sort -count
```

**What I got:** 228 total 403s, but again super spread out — highest count for any IP/page combo was only 3, across 20+ different IPs, all on normal store pages like `/cart.do`, `/product.screen`, `/category.screen`.

**What I concluded:** also nothing suspicious. Probably just users hitting things like expired sessions or normal permission edge cases, not actual unauthorized access attempts.

## What I actually took away from this
Both checks came back clean, and I think that's still a useful result, not a failed investigation. A big part of doing this kind of triage well is not jumping to "this is an attack" every time you see an error spike — you actually have to check whether it's concentrated around a few sources or spread out normally before deciding it means anything. This was good practice for that specific judgment call.

## What I'd try next time
- Narrow the time range (like per hour instead of the whole dataset) in case a short burst is getting averaged out
- Check the top IPs against a threat intel list if I had one
- Look for status codes like 401 if the dataset has them, since those tie more directly to failed logins