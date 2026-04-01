---
description: Track recent releases and announcements from competitors
allowed-tools: WebFetch, WebSearch, Read
argument-hint: "<competitor-name or all> [days-back: 30|60|90]"
---

Track the most recent product releases and announcements from: $1

## Step 1 — Determine Scope
If $1 is "all" or not provided, check all 9 competitors.
If a specific competitor is named, check only that competitor.
The lookback period is "$2" days (default to 60 days if not specified).

## Step 2 — Fetch Release Notes
For each competitor in scope, read the reference file from `${CLAUDE_PLUGIN_ROOT}/skills/competitor-intelligence/references/` to get the release notes URL(s), then use WebFetch to retrieve the latest entries.

Release notes URLs per competitor:
- Braze: https://www.braze.com/docs/releases/home
- MoEngage: https://developers.moengage.com/hc/en-us (navigate to "Release Notes" in sidebar) and https://help.moengage.com/hc/en-us (check "What's New" section)
- Bloomreach: https://documentation.bloomreach.com/engagement/ (look for Changelog in sidebar navigation)
- Klaviyo: https://developers.klaviyo.com/en/docs/changelog_ (API/SDK changes) and https://help.klaviyo.com/hc/en-us (product updates)
- Hightouch: https://hightouch.com/changelog
- Census/Fivetran Activations: https://fivetran.com/docs/activations and https://fivetran.com/blog
- Salesforce MC: https://help.salesforce.com/s/articleView?id=sf.mc_rn_home.htm
- CleverTap: https://docs.clevertap.com/docs/whats-new and https://developer.clevertap.com/docs/changelog
- Iterable: https://support.iterable.com/hc/en-us/categories/360002288712-Developer-and-API-Docs (search within for "release notes" or "what's new")

If a URL is inaccessible or returns an error, note this and use WebSearch to find recent announcements instead.

## Step 3 — Summarize Releases
For each competitor, produce:

### [Competitor Name]
**Releases tracked**: [date range]
**Source URL**: [URL used]

**Notable releases in the past $2 days:**
- [Feature/update 1]: [Brief description, date if available]
- [Feature/update 2]: [Brief description]
- ...

**Potential competitive implications for Insider One:**
- [1–2 sentences on how this release affects Insider's competitive position]

---

## Step 4 — Intelligence Summary
Produce a brief (1-paragraph) executive summary of the most competitively significant releases discovered across all checked competitors. Highlight anything that:
- Directly threatens a current Insider One strength
- Closes a gap where Insider was previously ahead
- Represents a new category or capability direction that is worth tracking

Always note the date this research was conducted and recommend re-running within 30 days.
