# Account Setup & Verification Checklist

Set up **Meta** and **Google** in **parallel**. The critical-path item on both is
**verification** — start those on day 1; everything else can proceed alongside.

> **"Payment + FB account + WABA" is not enough.** It lets you *start*, but without
> the verifications below you'll be capped (WhatsApp limited to test messages, Google
> serving paused) the moment you try to scale.

---

## 0. Prerequisites (do first, both platforms)

- [ ] Confirm you control DNS for both domains (`astrolove.carecompass`,
      `trueself.carecompass`) — required for domain verification.
- [ ] Gather legal-entity docs for **dreampost** (business registration / incorporation,
      address, official email) — both platforms will ask.
- [ ] Publish a **privacy policy** + **terms** on each site (required for WABA and
      reduces policy risk).
- [ ] Audit both landing pages against [`ad-policy-compliance.md`](./ad-policy-compliance.md)
      **before** sending any traffic — platforms review the destination, not just the ad.

---

## 1. Meta track

### 1a. Business Portfolio + Verification *(critical path — start today)*
- [ ] Create / confirm one **Business Portfolio** (Business Manager) for dreampost.
- [ ] Submit **Business Verification** (Business Settings → Security Center). **5–15
      business days.** Blocks WhatsApp template messaging and lifts ad-account/domain caps.

### 1b. Ad accounts (separate per brand — contains ban contagion)
- [ ] Create ad account **AstroLove**.
- [ ] Create ad account **TrueSelf**.
- [ ] Add a **separate payment method to each** (do not share one card across both).

### 1c. Domains & Pages
- [ ] Verify `astrolove.carecompass` (DNS TXT) under the portfolio.
- [ ] Verify `trueself.carecompass` (DNS TXT).
- [ ] Create / assign a **Facebook Page** per brand.

### 1d. WhatsApp (CTWA)
- [ ] Confirm the **WABA + Page + ad account** are all in the **same portfolio**
      (CTWA silently fails to save otherwise).
- [ ] Verify the WhatsApp **phone number**.
- [ ] Publish privacy-policy URL + build a **documented opt-in** that names the brand
      *and* the WhatsApp channel (lack of consent is the #1 cause of WABA restriction).

### 1e. Tracking
- [ ] Install **Meta Pixel** per site.
- [ ] Set up **Conversions API** (server-side) per site — pixel-only misses 50%+ of
      conversions. Use a shared `event_id` for browser+server dedup.

### 1f. API access (only if going API later)
- [ ] Create a Meta **App** (type: Business).
- [ ] Create a **System User** + non-expiring token (don't use 60-day user tokens for automation).
- [ ] Request **Advanced Access** to `ads_management` via App Review (~2 weeks) — needed
      for production-scale automation. `ads_read` + Standard Access is enough to start reading.

---

## 2. Google track *(in parallel)*

### 2a. Account structure
- [ ] Create a **Manager account (MCC)** for dreampost.
- [ ] Create **one sub-account per brand** (AstroLove, TrueSelf) under the MCC.

### 2b. Verification *(critical path — start today)*
- [ ] Complete **Advertiser Identity Verification** (gov ID and/or incorporation docs + country).
- [ ] Prepare for **Business Operations Verification** — this niche commonly triggers it
      (they'll ask for business model + registration). Can pause serving until done.

### 2c. Billing & tracking
- [ ] Set up **billing** per sub-account.
- [ ] Create **conversion actions** per site (+ **enhanced conversions** with hashed user
      data + GCLID).
- [ ] Confirm conversion tracking fires end-to-end on the live site (data appears in 3–6h).

### 2d. API access (only if going API later)
- [ ] Apply for a **developer token** from the MCC's API Center. **Approval takes days–weeks
      — apply now.** A live website URL + monitored contact email are required or it's rejected.
- [ ] Note access levels: **Basic** = 15,000 ops/day (fine for own accounts); **Standard** =
      unlimited (needed for third-party/distributed tools).
- [ ] Build/test against a **test account** immediately (no token approval needed) while the
      real token is in review. Note: test accounts can't do billing/conversions.

---

## 3. Visibility dashboard (Phase 0)

- [ ] Create a **Looker Studio** report.
- [ ] Add the **native Google Ads** connector (both sub-accounts).
- [ ] Add a **Meta connector** (Windsor.ai free/low tier, or Supermetrics) for both ad accounts.
- [ ] Build views split by **brand** + **platform**, with a date filter and a "last refreshed" note.

---

## Critical-path summary

| Item | Platform | Lead time | Blocks |
|------|----------|-----------|--------|
| Business Verification | Meta | 5–15 business days | WhatsApp templates, account/domain limits |
| Advertiser/Business Verification | Google | days–weeks | Ad serving |
| Developer token | Google | days–weeks | API automation only |
| App Review (`ads_management`) | Meta | ~2 weeks | API automation at scale only |

**Do today:** both verifications + (if going API) both access applications. Everything
else can run in parallel while these process.
