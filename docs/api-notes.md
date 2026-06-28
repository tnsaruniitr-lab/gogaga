# API Notes — Meta Marketing API & Google Ads API

Technical grounding for Phase 3 (API integration). **Versions and limits verified
current as of June 2026** — both platforms version aggressively, so re-check the live
docs before hardcoding anything.

---

## Meta Marketing API

- **Current version:** Graph API / Marketing API **v25.0** (released 2026-02-18).
  Pin it in the base URL: `https://graph.facebook.com/v25.0/...`
- **Cadence:** ~quarterly. Graph API versions last ~2 years, but **Marketing API
  features can be removed as little as 90 days** after a release. All versions prior to
  v24.0 deprecated 2026-06-09.
- **Object hierarchy:**
  `Business Portfolio → Ad Account (act_<id>) → Campaign → Ad Set → Ad → Ad Creative`
  - Endpoints: `POST /act_<id>/campaigns`, `/adsets`, `/ads`, `/adcreatives`
- **Objectives (ODAX, set on the Campaign):** `OUTCOME_AWARENESS`, `OUTCOME_TRAFFIC`,
  `OUTCOME_ENGAGEMENT`, `OUTCOME_LEADS`, `OUTCOME_SALES`, `OUTCOME_APP_PROMOTION`.
  Legacy enums (`LINK_CLICKS`, `CONVERSIONS`, `MESSAGES`…) are **rejected**. The delivery
  path (messages, leads, purchases) is chosen at the **ad-set** level
  (`optimization_goal` + `destination_type`/`promoted_object`).
- **Auth:** Meta App (type Business) + `ads_management` (write) / `ads_read` (read).
  Production automation → **System User token** (non-expiring), not a 60-day user token.
  **Advanced Access** to `ads_management` requires **App Review + Business Verification**.
- **Click-to-WhatsApp:** WABA + Page + ad account in the **same portfolio**; creative
  `object_story_spec.link_data.call_to_action` type `WHATSAPP_MESSAGE`
  (`app_destination = WHATSAPP`); run under `OUTCOME_ENGAGEMENT` with ad-set destination = WhatsApp.
  Send **CAPI for Business Messaging** with `ctwa_clid`, `action_source=business_messaging`.
- **Insights:** `GET /act_<id>/insights` (fields: impressions, clicks, spend, actions,
  action_values…; `level`, `time_increment=1`, `breakdowns`). Use **async report runs**
  for large pulls (poll `report_run_id`).
- **Rate limits (BUC):** per ad account, per use case. Read `X-Business-Use-Case-Usage`
  and `X-FB-Ads-Insights-Throttle`; back off on `estimated_time_to_regain_access`.
  Ads-management quota: Standard `300 + 40×active_ads`/hr; Advanced `100,000 + 40×active_ads`/hr.
- **Cost:** API is free; you pay ad spend only.
- **Watch-out:** Advantage+ Shopping/App (ASC/AAC) can no longer be created/updated via
  API as of v25.

---

## Google Ads API

- **Current version:** **v24.2** (released 2026-06-24). Pin a recent major (v24/v23).
- **Cadence:** faster as of 2026 (monthly minors, majors roughly quarterly); majors sunset
  after **~1 year**. v20 already sunset (2026-06-10). Schedule a recurring upgrade.
- **Three credentials to call production:**
  1. OAuth2 `client_id`/`client_secret` + `refresh_token`
  2. an **approved developer token** (from an MCC's API Center)
  3. `login-customer-id` header (the MCC id, **hyphens stripped**) when going through a manager
- **Developer-token access levels:** Test (test accounts only) · **Basic** (15,000 ops/day)
  · **Standard** (unlimited; required for third-party/distributed tools, gated by RMF policy).
- **Campaign hierarchy:**
  - Search: `CampaignBudget → Campaign → AdGroup → AdGroupAd` (RSA needs ≥3 headlines, ≥2
    descriptions) `+ AdGroupCriterion` (keywords)
  - Performance Max: `CampaignBudget → Campaign → AssetGroup → AssetGroupAsset` (no ad groups)
  - Create atomically via `GoogleAdsService.Mutate` with `MutateOperations` + temporary
    negative resource ids (budget→campaign→groups→ads in one request).
- **Budgets:** `amount_micros` (1,000,000 = 1 currency unit; e.g. 500000 = $5/day).
- **Reporting:** **GAQL** via `GoogleAdsService.Search` (paged) or `SearchStream` (bulk).
  `SELECT campaign.name, segments.device, metrics.impressions FROM campaign` — extra
  segments multiply row counts.
- **Client libraries:** official gRPC for Python, Java, PHP, .NET, Ruby (Perl = REST).
  Node.js / Go are community-maintained (no Google SLA).
- **Watch-out:** test accounts can't exercise billing / bid simulations / conversion
  uploads — validate those on a real account. A 200 on a conversion upload ≠ attribution
  (data appears in 3–6h, can be silently dropped on config conflict).

---

## Cross-cutting build rules (both platforms)

- **Pin versions** in config; calendar a quarterly upgrade ritual.
- **Re-pull a trailing ~30-day window** — both platforms restate history.
- **Money as integer micros** (Google native); convert Meta's decimals on ingest.
- **Idempotency key** on every mutation; **re-sync after every write**.
- **One throttled outbound client per platform**; back off on 429 / `RESOURCE_EXHAUSTED`.
- **Least privilege:** `ads_read` / read scopes until the write phase ships.
- **Store tokens encrypted** (KMS envelope encryption); never expose to the browser.
- **Server-side conversions** (Meta CAPI + Google enhanced conversions + GA4) with one
  shared `event_id` and one UTM taxonomy keyed on the `site` slug.

---

## Sources

Primary docs (re-verify before hardcoding):
- developers.facebook.com/docs/marketing-api/ · /docs/graph-api/changelog/versions/
- developers.google.com/google-ads/api/docs/ · /docs/sunset-dates · /docs/release-notes
- transparency.meta.com/policies/ad-standards/ (Personal Attributes)
- support.google.com/adspolicy/ (Misrepresentation)
