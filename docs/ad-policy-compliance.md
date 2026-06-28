# Ad-Policy Compliance — Astrology / Love / Self-Discovery

> **Read this before writing any ad copy.** Astrology/love/self-discovery is a
> high-enforcement niche. The ads are **allowed**, but careless copy → ad
> disapproval → repeat strikes → **account suspension**, which sets the whole
> program back weeks. This is the single biggest risk to the launch.

Verify exact current wording in the **Google Advertising Policies Help Center** and
**Meta Transparency Center** before finalizing — these policies change often (a March
2026 Meta enforcement update materially tightened things).

---

## The one rule that prevents most rejections

> **Describe the PRODUCT and the INSIGHT — never the VIEWER.**

Both platforms reject copy that *implies you know something about the person seeing
the ad*. Astrology copy naturally drifts into this ("your sign", "are you a Scorpio",
"struggling in love?") — that's the trap.

---

## Meta — Personal Attributes policy (the #1 trap)

Meta bars ads that **assert or imply** a viewer's personal attributes — **directly or
indirectly**. Relevant categories for this niche: **religion/beliefs** (astrology
itself counts), **relationship status**, **mental/physical health**, **sexual
orientation**.

The **March 2026** update added AI "semantic intent detection" — it now flags
*indirect* implication, conditional framing, and empathy hooks, even with zero banned
words. Imagery is read together with text (a "found my soulmate" testimonial image can
be flagged on its own).

### ❌ Non-compliant → ✅ Compliant rewrites

| ❌ Don't | Why it fails | ✅ Do instead |
|----------|-------------|--------------|
| "Are you a Scorpio unlucky in love?" | implies religion/belief + relationship status | "Explore astrology-based compatibility insights" |
| "Discover what **YOUR** sign says about **your** love life" | implies viewer's beliefs + relationship | "Daily horoscopes & birth-chart readings — for entertainment" |
| "Single and searching? Your soulmate is near" | implies relationship status + guaranteed outcome | "Discover your astrology profile with TrueSelf" |
| "Heal your heartbreak with a personal reading" | implies emotional state + healing claim | "A self-discovery app rooted in astrology" |
| "Feeling lost after a breakup?" | empathy/vulnerability hook (now banned) | "Astrology insights for relationships and self-growth" |
| "We understand your pain" | implied targeting / exploiting hardship | (remove — describe the product) |

---

## Google — Misrepresentation policy

Astrology is **not** on Google's prohibited list, but the **Misrepresentation**
policy bites:

- **Unreliable claims:** no guaranteeing improbable results ("guaranteed soulmate",
  "change your destiny").
- **Unacceptable business practices:** no exploiting fears/hardship, no concealing the
  nature of the business, no hidden/auto-enroll subscriptions.
- **Advertiser Identity Verification** is rolling out to all advertisers; this niche
  may also trigger **Business Operations Verification**.

---

## Universal do / don't (both platforms + FTC)

**Don't, anywhere in the funnel (ad *and* landing page):**
- Guarantees: "guaranteed soulmate", "we'll fix your love life", "remove the block keeping you single"
- Healing/miracle: "heal", "cure", "remove curse", "break generational curses"
- Fear/destiny: "change your destiny", scare-then-upsell
- Second-person attribute hooks: "are you…", "your sign", "struggling with…", "we understand…"
- Hidden subscriptions, fake countdowns, "free reading" that auto-enrolls into recurring charges

**Do:**
- Frame around the product/benefit/insight.
- Add **"For entertainment purposes only"** on ads (where space allows) and prominently
  on both landing pages.
- Make pricing, recurring billing, cancellation, and business identity **explicit**.
- Launch a **small, clearly-compliant** creative set first to build account standing,
  then scale.
- Keep ad + landing page **consistent** — both get reviewed.

---

## Banned-phrase linter (use before every submission)

Block any creative containing these, pre-submission:

```
guarantee            soulmate guaranteed   heal              cure
remove block         remove curse          break ... curse   change destiny
change your destiny  are you               your sign         struggling with
we understand        feeling lost          before/after      manifest your soulmate
fix your love life   destined              we know           for people dealing with
```

> If you build the Phase 3 app, implement this list as an automated check on the
> `campaign_action` create path so non-compliant creative can't be submitted via API.

---

## Account-structure defenses (compliance is also structural)

- **Separate ad accounts + separate payment methods** per brand → a strike on one
  brand can't take down the other ("ban contagion").
- **Fix/appeal promptly** rather than relaunching near-identical creative — repeat
  violations escalate from ad disapproval to **account suspension** fast in this category.
- Keep a clean track record early; manual reviewers weight account history heavily here.
