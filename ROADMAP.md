# Roadmap — dreampost Ad Operations

Two brands (**AstroLove**, **TrueSelf**), two ad platforms (**Meta**, **Google**),
launched **in parallel**, with one dashboard for visibility and an optional path
to API-driven control.

---

## The core decision: visibility vs control

"Single dashboard" means two different things. Be clear which you're buying/building:

| | **Visibility** (see results) | **Control** (create/pause/edit budgets) |
|---|---|---|
| Nature | Reporting only | Operational |
| Fastest path | Looker Studio + connector — **days, ~$0–25/mo** | Buy a tool **or** build custom |
| API work | Connector handles it | You write against both ad APIs |
| Maintenance | ~None | Permanent (APIs break on a schedule) |

**Recommendation:** *Buy* the visibility layer immediately. Treat API-driven
*control* as a separate, later project — and only build it if an off-the-shelf
operational tool (Adzooma ~$99/mo, Optmyzr ~$249/mo) genuinely can't do the job.
For a 2-site brand, a fully custom dashboard runs ~$80–100k over 3 years plus
continuous maintenance, because both APIs sunset aggressively.

---

## Phases

### Phase 0 — Visibility dashboard *(week 1, ~$0–25/mo, no code)*

One dashboard across both channels and both brands.

- **Google Looker Studio** (free) + **native Google Ads connector**.
- **Meta** data via a third-party connector — start with **Windsor.ai** (free / ~$19–23/mo)
  or **Supermetrics** (~$37/mo+). Google has no native Meta connector.
- Build one report: spend, clicks, CTR, CPC, conversions, CPA, ROAS — split by
  **brand** (astrolove / trueself) and **platform**, with a date filter.
- Add a "last refreshed" note so stakeholders know data freshness.

> Watch-out: Looker Studio blends a **max of 5 data sources** per blend (not raised by
> upgrading), and connector pricing scales **per ad account** — two brands likely = more
> accounts = higher connector cost. Budget for it.

### Phase 1 — Account foundation *(weeks 1–3, both platforms in parallel)*

The long pole is **verification**. Start day 1. Full steps in
[`docs/account-setup-checklist.md`](./docs/account-setup-checklist.md). Summary:

**Meta track**
- One **Business Portfolio** (Business Manager) for dreampost.
- Complete **Business Verification** (5–15 business days — blocks WhatsApp
  templates and lifts account/domain limits).
- **Two separate ad accounts** (astrolove, trueself) + **separate payment method each**
  → contains "ban contagion".
- **Verify each root domain** (DNS TXT).
- Link the **WABA + Facebook Page + ad account** in the **same** portfolio (required
  for Click-to-WhatsApp).
- Install **Meta Pixel + Conversions API** per site.

**Google track (in parallel)**
- One **Manager (MCC) account** → one **sub-account per brand**.
- Complete **Advertiser Identity Verification**; prepare incorporation/business-model
  docs in case **Business Operations Verification** triggers (likely for this niche).
- Set up **billing** per account and **conversion actions** (+ enhanced conversions).
- Apply for a **Google Ads API developer token** *now* if you intend to go API later
  (approval takes days–weeks; the application itself doesn't block manual launch).

### Phase 2 — First campaigns live *(weeks 2–4, manual, both platforms)*

Launch through the **native Ads Managers** (Ads Manager / Google Ads UI) while
verification finishes. No custom code needed to start spending.

- **Meta:** lead with **Click-to-WhatsApp** (objective `OUTCOME_ENGAGEMENT`, ad-set
  destination = WhatsApp) — you already have the WABA. Also test Traffic/Leads.
- **Google:** **Search** for high-intent keywords + a small **Performance Max** test.
- **Compliance gate:** every creative + landing page must pass
  [`docs/ad-policy-compliance.md`](./docs/ad-policy-compliance.md). In this niche, a
  bad launch = account suspension, which sets everything back weeks.
- Start small and clean to **build account standing** before scaling.

### Phase 3 — API integration / custom control plane *(optional, later)*

Only if Phase 0 + an operational tool can't meet your needs. Reference
architecture below.

---

## Reference architecture (Phase 3, if you build)

A single-tenant, multi-account "ad-ops cockpit" with three planes.

```
                ┌────────────────────────────────────────────────┐
                │  Next.js dashboard (RBAC: viewer/operator/admin) │
                └───────────────┬───────────────┬─────────────────┘
                                │               │
                   READ plane   │               │   WRITE plane
        ┌───────────────────────▼──┐      ┌─────▼───────────────────────┐
        │ Scheduled sync (BullMQ)   │      │ Audited action queue (BullMQ)│
        │ • Google GAQL (SearchStream)│    │ • create / pause / set_budget│
        │ • Meta Insights (time_inc=1)│    │ • validate_only → call → re- │
        │ • UPSERT → Postgres        │      │   sync entity               │
        └───────────────────────┬──┘      └─────┬───────────────────────┘
                                │               │
                        ┌───────▼───────────────▼───────┐
                        │ Postgres (system of record)    │
                        │ normalized, platform-agnostic  │
                        └───────────────┬───────────────┘
                                        │
                       MEASUREMENT plane │ (server-side)
            Meta CAPI + Google Enhanced Conversions + GA4 (shared event_id, one UTM scheme)
```

**Stack (1–2 engineers):** Next.js (App Router) + TypeScript backend (NestJS/Fastify)
+ Postgres 16 + Redis/BullMQ (cron syncs + write queue) + cloud KMS (envelope-encrypt
OAuth tokens). Resist multi-tenant / microservice / BigQuery complexity at this scale —
indexed Postgres handles two ad accounts comfortably.

**Unified data model (platform discriminator + per-site attribution):**

```
site(id, slug[astrolove|trueself], domain, ga4_property_id, meta_pixel_id, gads_conversion_customer_id)
platform_account(id, platform[google|meta], external_account_id, mcc_or_business_id, currency, timezone)
campaign(id, platform, account_id→, external_id, name, status, objective, daily_budget_micros, site_id→)
ad_group(id, platform, campaign_id→, external_id, name, status, optimization_goal, budget_micros?)  -- unifies Meta ad_set + Google ad_group
ad(id, platform, ad_group_id→, external_id, name, status, creative_ref)
daily_metric(id, platform, level, entity_id, site_id→, date, impressions, clicks, spend_micros,
             conversions, conversion_value_micros, raw_payload JSONB)   -- UPSERT key: (platform, level, entity_id, date)
oauth_credential(id, platform_account_id→, encrypted_refresh_token, ...)  -- envelope-encrypted via KMS
campaign_action(id, actor_user_id, platform, target_entity, action_type, request_payload, idempotency_key, status, response)  -- append-only audit log
sync_run(id, account_id→, job_type, window, started, finished, rows_upserted, status, error)
```

Money is stored as integer **micros** (Google's native unit); convert Meta's decimal
currency on ingest. Keep `raw_payload` JSONB so no field is lost across version upgrades.

**Non-negotiable build rules** (each one is a real bug if skipped):
- **Re-pull a trailing 30-day window** daily + hourly today/yesterday — both platforms
  *restate* historical conversions/spend.
- **Re-sync after every write** — never show optimistic UI state; the platform may
  reject or auto-modify (min budgets, learning phase).
- **Idempotency key on every mutation** — Google has partial-failure mode; Meta returns
  per-item errors.
- **Centralized, throttled API client per platform** — back off on Meta's
  `X-Business-Use-Case-Usage` / `X-FB-Ads-Insights-Throttle` and Google's
  `RESOURCE_EXHAUSTED` (429).
- **Read-only scopes** (`ads_read`) until the write phase ships; add `ads_management`
  only when needed.
- **Pin API versions** in config (Meta v25.0, Google v24.2) + a **scheduled upgrade
  ritual** — both sunset aggressively.

---

## Build-vs-buy quick reference

| Need | Buy this | Approx cost |
|------|----------|-------------|
| See metrics, both channels | Looker Studio + Windsor.ai/Supermetrics | $0–37/mo |
| Prettier client-style reports | AgencyAnalytics | ~$79/mo |
| **Control** campaigns from one place | Adzooma (free → ~$99/mo) or Optmyzr (~$249/mo) | $0–249/mo |
| Custom blended metrics tools can't model | Build (Phase 3) — managed stack preferred | $$$$ + maintenance |

---

## Open items / decisions still to make

- [ ] Confirm `.carecompass` domains resolve and you control DNS (needed for verification).
- [ ] Decide: stop at **buy** (Phase 0–2) or commit to the **custom build** (Phase 3)?
- [ ] Confirm legal entity + incorporation docs are ready for both platforms' verification.
- [ ] Decide WhatsApp role: is CTWA the primary conversion path, or a secondary channel?
