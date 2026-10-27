# 📊 Basic Conversion Tracking Plan – LeafLoop Tea Co.

> **Digital Marketing Track task:** Define important website conversions for a business and explain how each should be tracked.
> **Objective:** Understand how business outcomes are measured.

**Tools:** Google Analytics 4 · Google Tag Manager · Google Sheets
**Author:** Reddi Malli Nandini

---

## 🏪 The business (case study)

**LeafLoop Tea Co.** is a fictional D2C online tea store based in Hyderabad. It sells packaged teas and gift boxes online, and also takes bulk / corporate-gifting orders through an enquiry form.

**Business goals**
1. Increase online revenue from tea orders
2. Generate bulk and corporate-gifting leads
3. Grow a repeat-customer base (accounts + newsletter)

---

## 📁 Repository structure

```
.
├── README.md
├── docs/
│   └── Conversion_Tracking_Plan_Report.pdf   # full written report
├── sheets/
│   └── conversion-tracking-plan.xlsx         # the plan (open in Google Sheets)
├── gtm/
│   ├── gtm-container-import.json             # importable GTM container
│   └── datalayer-snippets.js                 # code the developer adds to the site
└── demo-site/
    └── index.html                            # practice shop to test events
```

The spreadsheet has 7 tabs: **Overview, Conversion List, Event Definitions, Tracking Plan, Conversion Priorities, KPI Targets, QA Checklist.**

---

## 🎯 Deliverable 1 – Conversion list

### Primary conversions (marked as GA4 Key events)
| ID | Conversion | GA4 event | What counts |
|----|-----------|-----------|-------------|
| C1 | Online purchase | `purchase` | Payment completed for an order |
| C2 | Corporate / bulk enquiry | `generate_lead` | Corporate-gifting form submitted **successfully** |
| C3 | Account sign-up | `sign_up` | Customer account created |

### Micro-conversions (tracked, not counted as conversions)
| ID | Conversion | GA4 event |
|----|-----------|-----------|
| M1 | Begin checkout | `begin_checkout` |
| M2 | Add to cart | `add_to_cart` |
| M3 | Add shipping info | `add_shipping_info` |
| M4 | Add payment info | `add_payment_info` |
| M5 | Newsletter sign-up | `newsletter_signup` |
| M6 | WhatsApp chat click | `whatsapp_click` |
| M7 | Click to call | `click_to_call` |
| M8 | View product | `view_item` |
| M9 | Catalog download | `file_download` |
| M10 | Site search | `search` |

**Why separate them?** Primary conversions create real business value (money, leads, customers). Micro-conversions show intent and help find where people drop off. Counting micro-conversions as conversions would inflate results and send the wrong signal to Google Ads.

---

## 🧾 Deliverable 2 – Event definitions

| Event | Fires when | Key parameters | How it's tracked |
|-------|-----------|----------------|------------------|
| `purchase` | After payment gateway confirms success (once per order) | transaction_id, value, currency, tax, shipping, coupon, items | dataLayer → GTM |
| `generate_lead` | Only after the server confirms the form was submitted | form_name, lead_type, value, currency | dataLayer → GTM |
| `sign_up` | Account created | method | dataLayer → GTM |
| `begin_checkout` | Checkout page loads from cart | value, currency, coupon, items | dataLayer → GTM |
| `add_to_cart` | Item added to cart | value, currency, items | dataLayer → GTM |
| `add_shipping_info` | Address step submitted | shipping_tier, items | dataLayer → GTM |
| `add_payment_info` | Payment method chosen | payment_type, items | dataLayer → GTM |
| `view_item` | Product page loads | value, currency, items | dataLayer → GTM |
| `newsletter_signup` | Newsletter form success | form_location | dataLayer → GTM |
| `whatsapp_click` | Click on a `wa.me` link | link_url | GTM Link Click trigger |
| `click_to_call` | Click on a `tel:` link | link_url | GTM Link Click trigger |
| `file_download` | PDF catalog downloaded | file_name, file_extension | GA4 Enhanced Measurement |
| `search` | Site search used | search_term | GA4 Enhanced Measurement |

**Naming rules:** GA4 *recommended* event names are used wherever possible so built-in ecommerce & lead reports work automatically. Custom events use `snake_case`, under 40 characters.

---

## 🛠️ Deliverable 3 – Tracking plan (how it's set up)

```
Website action ──► dataLayer.push({event: "purchase", ...})
                        │
                        ▼
          GTM Custom Event trigger  "CE - purchase"
                        │
                        ▼
          GA4 Event tag  "GA4 - Event - purchase"
                        │
                        ▼
          GA4 property ──► marked as Key event ──► Google Ads (primary)
```

| GTM tag | Trigger | Key event? |
|---------|---------|-----------|
| GA4 - Config (Google tag) | All Pages | – |
| GA4 - Event - purchase | Custom Event = `purchase` | ✅ |
| GA4 - Event - generate_lead | Custom Event = `generate_lead` | ✅ |
| GA4 - Event - sign_up | Custom Event = `sign_up` | ✅ |
| GA4 - Event - begin_checkout / add_to_cart / add_shipping_info / add_payment_info / view_item | Custom Event = event name | ❌ |
| GA4 - Event - newsletter_signup | Custom Event = `newsletter_signup` | ❌ |
| GA4 - Event - whatsapp_click | Just Links: Click URL contains `wa.me` | ❌ |
| GA4 - Event - click_to_call | Just Links: Click URL starts with `tel:` | ❌ |
| *(automatic)* | GA4 Enhanced Measurement: downloads, search, scroll | ❌ |

Full details (variables, notes) are in the **Tracking Plan** tab of the spreadsheet.

---

## 🏆 Deliverable 4 – Conversion priorities

| Rank | Event | Tier | Why it matters | Google Ads |
|------|-------|------|----------------|-----------|
| 1 | `purchase` | Tier 1 – Primary (revenue) | Direct revenue, main ROAS KPI | Primary |
| 2 | `generate_lead` | Tier 1 – Primary (lead) | One corporate order can be 20–50× a normal order | Primary |
| 3 | `sign_up` | Tier 2 – Secondary | Drives repeat purchases & remarketing | Secondary |
| 4–7 | `begin_checkout`, `add_to_cart`, `add_payment_info`, `add_shipping_info` | Tier 3 – High-intent micro | Find checkout drop-off, cart remarketing | – |
| 8–11 | `whatsapp_click`, `newsletter_signup`, `click_to_call`, `file_download` | Tier 4 – Engagement micro | Pre-purchase questions, owned audience, B2B interest | – |
| 12–13 | `view_item`, `search` | Tier 5 – Diagnostic | Product interest & what people look for | – |

---

## 🚀 How to use this

1. **GA4:** Create a property + web data stream, copy the Measurement ID, turn on Enhanced Measurement.
2. **GTM:** Create a web container → *Admin → Import Container* → choose `gtm/gtm-container-import.json` → **Merge**.
3. Open the variable **`GA4 Measurement ID`** and replace `G-XXXXXXXXXX` with your ID.
4. Add the code from `gtm/datalayer-snippets.js` to the website at the right moments.
5. Test with **GTM Preview** and **GA4 DebugView** (checklist in the *QA Checklist* tab), then **Publish**.
6. In GA4 *Admin → Events*, mark `purchase`, `generate_lead`, `sign_up` as **Key events**.
7. Register custom dimensions: `form_name`, `lead_type`, `form_location`, `method`.

### 🧪 Try the demo
`demo-site/index.html` is a small practice shop. Each button sends the exact dataLayer event from this plan and shows it in an on-page log.
To test with real GTM: paste your GTM ID into the snippet at the top of the file, host it (e.g. GitHub Pages), and open it in GTM Preview mode.

---

## 💡 Key learnings

- Measure **business outcomes** (sales, leads), not just page views.
- Keep **primary conversions** separate from **micro-conversions** so reports stay honest and ads get the right signal.
- Fire conversions **on success**, not on button click, so failed forms and payments aren't counted.
- Using **GA4 recommended event names** unlocks built-in reports with less setup.
- A written tracking plan keeps marketers, developers and analysts aligned.

---

*Note: LeafLoop Tea Co. is a fictional business created for this exercise. All IDs (GTM-XXXXXXX, G-XXXXXXXXXX) and numbers are placeholders.*
