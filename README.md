# dreampost — Ad Operations

Paid-ads operations and unified reporting for two brands owned by **dreampost**:

| Brand | Site | Theme |
|-------|------|-------|
| AstroLove | astrolove.carecompass | Astrology-based compatibility / relationships |
| TrueSelf | trueself.carecompass | Astrology-based self-discovery |

## Goal

Run paid campaigns on **Meta Ads** and **Google Ads** (in parallel), with **one
dashboard** for cross-channel visibility, and a path toward **API-driven**
campaign management.

## What's in this repo

This repo currently holds the **plan**, not the application. Start here:

| File | Purpose |
|------|---------|
| [`ROADMAP.md`](./ROADMAP.md) | Phased build plan + reference architecture + build-vs-buy decision |
| [`docs/account-setup-checklist.md`](./docs/account-setup-checklist.md) | Step-by-step account, billing & verification setup for both platforms |
| [`docs/ad-policy-compliance.md`](./docs/ad-policy-compliance.md) | **Critical.** Astrology/love-niche ad-policy rules + banned-phrase linter |
| [`docs/api-notes.md`](./docs/api-notes.md) | Current API versions, auth model, key facts & gotchas (mid-2026) |
| [`docs/ideas-why-now.md`](./docs/ideas-why-now.md) | 8 adjacent product ideas ranked by **"why now"** timing strength (deep-research, mid-2026) |
| [`docs/whatsapp-voice-moat.md`](./docs/whatsapp-voice-moat.md) | Biggest **WhatsApp-voice** opportunity that Meta **won't absorb** + the defensible infra play (deep-research, mid-2026) |

## TL;DR recommendation

1. **Visibility this week:** Looker Studio (free) + a Meta connector. Don't build a
   reporting dashboard from scratch.
2. **Start verification today:** "payment + FB + WABA" is **not** enough. Meta
   Business Verification (5–15 business days) and Google Advertiser Verification
   gate everything — they're the long pole.
3. **Launch both platforms in parallel** via the native Ads Managers while
   verification completes.
4. **Niche risk is the #1 threat.** Astrology/love ads get flagged hard. Read
   `docs/ad-policy-compliance.md` before writing a single line of ad copy.
5. **Custom API control plane is optional** — only build it if off-the-shelf
   tooling can't do what you need (see ROADMAP Phase 3).

> Research basis: API versions and policies verified current as of **June 2026**
> (Meta Marketing API v25.0, Google Ads API v24.2). Both platforms version
> aggressively — re-check the live docs before hardcoding anything.
