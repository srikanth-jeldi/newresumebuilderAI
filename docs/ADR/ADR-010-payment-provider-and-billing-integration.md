# ADR-010 — Payment Provider and Billing Integration

Status: ACCEPTED

## Context
Resumaire is an India-first startup product with a strict cost constraint. The product will offer a free plan and later paid plans/usage tiers. Billing architecture must support recurring subscriptions, upgrades/downgrades, cancellations, payment verification, webhooks, entitlement updates and future international expansion without locking core product logic to one provider.

## Decision
Use **Razorpay as the primary India payment provider** for MVP/early production, behind a provider abstraction. Keep **Stripe as a future international/secondary provider** rather than an MVP dependency.

### Why Razorpay First
- India-first onboarding and payment ecosystem fit.
- Supports recurring subscriptions through cards, UPI Autopay and eMandate where available.
- Avoids depending on Stripe for MVP because new Stripe accounts in India are currently invite-only.
- Lets Resumaire launch with an India-friendly billing path while keeping business logic provider-neutral.

## Architecture

```text
Web / Mobile
    |
    v
Billing API / Entitlement Layer
    |
    +--> PaymentProvider interface
           |
           +--> RazorpayProvider   # MVP
           +--> StripeProvider     # future

Provider webhook
    |
    v
Webhook verifier
    |
    v
Billing event processor
    |
    +--> subscription state
    +--> payment state
    +--> entitlement state
    +--> usage limits
```

## Ownership
For MVP, billing/entitlement logic may live inside Identity Service or a small dedicated billing module owned by it. Do **not** create a separate Billing microservice until billing complexity or team/deployment boundaries justify it.

Identity/Billing owns:
- plan catalog metadata
- subscription record
- provider customer ID
- provider subscription ID
- billing state
- entitlement snapshot/version
- payment event references
- cancellation/renewal state

AI Career Service and Resume Service must not query Razorpay directly. They ask the entitlement/usage layer whether an operation is allowed.

## Provider Abstraction

Define an internal interface similar to:

```text
PaymentProvider
- createCustomer(...)
- createSubscription(...)
- cancelSubscription(...)
- pauseSubscription(...)       # only if product policy uses it
- resumeSubscription(...)      # only if product policy uses it
- fetchSubscription(...)
- verifyWebhook(...)
- normalizeEvent(...)
```

Provider SDK response objects must not leak into domain/business code.

## Subscription States
Internal normalized states:
- TRIALING
- ACTIVE
- PAST_DUE
- PAUSED
- CANCEL_PENDING
- CANCELLED
- EXPIRED

Provider-specific states must be mapped into these internal states.

## Payment States
- CREATED
- AUTHORIZED
- CAPTURED
- FAILED
- REFUNDED
- PARTIALLY_REFUNDED

Only authoritative provider confirmation/webhook processing may grant paid entitlements.

## Webhook Rules
Webhooks are security-sensitive and mandatory for authoritative state reconciliation.

Required:
- verify provider webhook signature
- reject invalid signatures
- persist provider event ID
- make processing idempotent
- ignore/reconcile duplicate events
- do not trust frontend success callback as final payment truth
- record processing status and failure reason
- retry safe failed event processing
- keep webhook secret outside source control

## Entitlement Flow

```text
User starts PRO purchase
↓
Backend creates provider subscription/order
↓
Frontend opens provider-owned checkout
↓
Payment completes
↓
Provider webhook reaches backend
↓
Signature verified
↓
Subscription updated ACTIVE
↓
Entitlement snapshot updated
↓
PRO limits enabled
```

Do not grant paid access solely because the browser/mobile client says payment succeeded.

## Web Checkout
For web MVP, use Razorpay's hosted/provider-owned checkout rather than building card/UPI forms ourselves.

Resumaire should never collect/store raw card details.

## Mobile Billing Warning
Android/iOS native apps may be subject to Google Play / Apple in-app purchase rules for digital subscriptions/features distributed through the stores.

Therefore:
- Web billing uses Razorpay for India MVP.
- Mobile paid-plan purchase flow must be reviewed against current Google Play and Apple App Store billing rules before production release.
- Do not automatically embed Razorpay checkout inside store-distributed mobile apps for digital subscriptions without policy review.
- Mobile clients can consume entitlement state from backend regardless of where the valid purchase originated.

## Free and Paid Plan Architecture
Plan and quota configuration is server-side and versioned.

Example plan keys:
- FREE
- PRO
- TEAM (future)

Each plan maps to entitlements such as:
- max active resumes
- ATS analyses/month
- field AI suggestions/month
- full tailoring runs/month
- interview questions/month
- mock interviews/month
- exports/month
- storage quota
- premium template access

Do not hard-code limits in web/mobile clients.

## Pricing Strategy
Exact INR pricing is **not** an architecture constant.

Before paid launch:
1. Run beta/free usage.
2. Measure average AI cost per user.
3. Measure document conversion/storage cost.
4. Measure high-percentile heavy-user cost.
5. Add payment-provider fees, infra, tax/compliance and margin.
6. Set pricing from real unit economics.

Keep prices in server-managed product/plan configuration, not source scattered across clients.

## Failure Scenarios
### Payment succeeds but webhook delayed
Show `Payment verification in progress` and reconcile asynchronously.

### Frontend closes after payment
Webhook still updates subscription.

### Duplicate webhook
Idempotent event processing; no duplicate entitlement grants.

### Renewal fails
Move to PAST_DUE/grace policy, then downgrade according to product rules.

### Cancellation
Retain paid access until the paid-through date if cancellation-at-period-end is selected.

### Provider outage
Existing entitlements remain available according to cached/persisted subscription state; do not block already-paid users merely because Razorpay API is temporarily unavailable.

## Refunds
Refund handling must update payment records and, when applicable, subscription/entitlement status. Exact refund policy is a product/legal decision and must be displayed clearly before paid launch.

## Security
- payment secrets only in secret manager/environment configuration
- webhook signature verification mandatory
- never log secrets or raw payment credentials
- no raw card storage
- least-privilege API credentials
- audit subscription state changes
- require authenticated ownership for billing portal/subscription operations

## Cost Discipline
MVP does not need:
- a dedicated billing microservice
- a paid subscription-management SaaS on top of Razorpay
- a custom card-entry UI
- multi-provider routing

Add complexity only when usage/revenue justifies it.

## Future International Expansion
When international demand is material:
- add StripeProvider or another provider behind the same interface
- keep Resumaire subscription/entitlement model unchanged
- map provider-specific webhooks into normalized events

Do not migrate business logic to provider-specific terminology.

## Consequences
### Positive
- practical India-first launch path
- low initial complexity
- provider lock-in reduced
- recurring billing supported
- secure provider-owned checkout
- future global expansion possible

### Negative
- provider abstraction adds some engineering work
- mobile store billing requires a separate policy/implementation review
- subscription webhook reconciliation must be implemented correctly

## Revisit Triggers
Revisit when:
- international customers become material
- Stripe/another provider is needed for global billing
- mobile subscriptions go live in app stores
- billing complexity justifies a dedicated Billing Service
- enterprise invoicing is introduced

## Final Decision
**Razorpay primary for India web billing; provider-neutral entitlement architecture; Stripe/global provider later; native mobile billing decided separately under app-store rules.**
