---
name: product_admin
description: Nova product-admin MCP — read before preview_document / tools/call.
---

# Product Admin MCP — operator guide

Read this resource before calling tools on **Product Admin** (`https://nova.civai.co/mcp/agents/product-admin`).

## Auth and scope

| Item | Detail |
|------|--------|
| **Auth** | Programmatic Nova API key JWT — `Authorization: Bearer <token>` or `X-API-Key` |
| **Setup** | https://nova.civai.co/api-keys |
| **Owner scope** | Every tool runs as the signed-in Nova account email. You only see **your** products (`data/content_products/{owner}/`). |
| **Not in scope** | Other owners' products. Public pay-page reads (those are unauthenticated REST). |

**Tool names** are namespaced: `product_admin__<action>`.

**Mutating tools** (create/update/delete/set_*_config/upload/rotate/save/approve/reject/assign/dismiss) require **Yes/No approval** in MCP hosts that support elicitation.

---

## Mental model

```
Nova owner (API key)
  └── Product Admin MCP
        └── product_admin__*  → products CRUD, payment/logistics/mobile-push config,
                                  icons, webhook secrets, bank onboarding, deposits,
                                  bank-credit reconciliation
```

This is the Products page of the Nova workspace (`workspace?section=products`), 1:1.

**product_id** — copy verbatim from `list_products` / `get_product`. Never guess.
**deposit_id** / **credit_id** — copy verbatim from `list_deposits` / `list_bank_credits`.

---

## Typical flows

### Read a product
1. `list_products` — all products for the owner.
2. `get_product { product_id }` — full detail (payment_config, logistics_config, mobile_push, icon_url).

### Turn on bank-transfer payments
1. `list_products` or `create_product { id, name, site_url }` if new.
2. `set_payment_config { product_id, payment_config: { enabled: true, method: "nigeria_bank_transfer", catalog_mode: "skus", skus: [...] } }` — merges onto existing payment_config.
3. `save_merchant_bank { bank_name, account_name, account_number, auto_approve_enabled, product_id }` — sets the receiving bank account and provisions the alert-forwarding inbox.
4. `get_onboarding_status { product_id }` to confirm.

### Webhook secret
- `get_webhook_secret { product_id }` to read.
- `rotate_webhook_secret { product_id }` to rotate (old secret stops verifying — approval required).

### Deposits (payment transactions)
1. `list_deposits { product_id, status: "pending" }`.
2. `approve_deposit { product_id, deposit_id }` — runs fulfillment.
3. `reject_deposit { product_id, deposit_id, reason }`.
4. `resend_deposit_fulfillment { product_id, deposit_id }` — retry fulfillment delivery.
5. `set_deposit_refund_status { product_id, deposit_id, refund_status, note }`.

### Bank-credit reconciliation
1. `list_bank_credits { status: "unmatched" }`.
2. `assign_bank_credit { credit_id, deposit_id }` to link a credit to a pending deposit.
3. `dismiss_bank_credit { credit_id }` if it is not a sale.

### Icons
- `upload_product_icon { product_id, image_base64, content_type }` (JPEG/PNG/WebP, max 2MB).
- `remove_product_icon { product_id }`.

---

## Action reference

| Action | Approval | Notes |
|---|---|---|
| `list_products` | no | |
| `get_product` | no | |
| `list_twitter_accounts` | no | connected X accounts usable as an `x_post` publish destination |
| `create_product` | yes | id, name, site_url required |
| `update_product` | yes | partial patch; `content_enabled` gates the pipeline, `publish_destinations` sets where publish sends content — see Content Pipeline Agent for actually running scan/generate/publish |
| `delete_product` | yes | irreversible |
| `set_payment_config` | yes | merges onto existing payment_config |
| `set_logistics_config` | yes | |
| `set_mobile_push_config` | yes | |
| `upload_product_icon` | yes | base64, max 2MB |
| `remove_product_icon` | yes | |
| `get_webhook_secret` | no | |
| `rotate_webhook_secret` | yes | invalidates old secret |
| `get_onboarding_status` | no | |
| `save_merchant_bank` | yes | 10-digit NUBAN account_number |
| `save_alert_sources` | yes | allowlist of bank-alert sender emails |
| `rotate_inbox` | yes | old forwarding address stops matching |
| `list_bank_alert_guides` | no | |
| `test_parse_bank_alert` | no | debug the alert parser |
| `list_deposits` | no | |
| `approve_deposit` | yes | runs fulfillment |
| `reject_deposit` | yes | |
| `set_deposit_refund_status` | yes | |
| `resend_deposit_fulfillment` | yes | retries delivery |
| `list_bank_credits` | no | |
| `assign_bank_credit` | yes | |
| `dismiss_bank_credit` | yes | |
