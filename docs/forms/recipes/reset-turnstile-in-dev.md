---
title: Reset Turnstile when you're locked out in dev
summary: One admin action clears the per-tenant counters.
order: 11
recipe: true
---

# Reset Turnstile when you're locked out in dev

When you'd use this — you've been hammering a public form in dev,
anti-abuse flagged your IP, and Turnstile is now serving a managed
challenge or the per-IP token bucket is empty. You want a clean slate
without waiting for the refill window or 00:00 UTC.

## 1. Clear the counters

In the admin shell, open **Settings → Anti-abuse** and click **Reset
counters**. Confirm in the modal. The action clears the per-tenant
daily submission count, the per-IP token-bucket state for every form
slug, and the short-lived Turnstile challenge history. It does not
touch submission rows or any audit ledger — only the counters that
gate future requests.

## 2. Submit again

The next submission from the same browser passes. Turnstile re-runs
its invisible-first check from a clean slate, and the per-IP throttle
starts the bucket full again at the burst allowance for your tier.

Counters are tenant-scoped, so this only affects your own tenant —
other tenants on the same instance are untouched. Use this freely in
dev; in production it's still safe (the only state you're discarding
is rate-limit accounting), but you'll want to know why you were
throttled before clearing.

## See also

- [Anti-abuse tuning tutorial](/docs/forms/tutorials/301-anti-abuse)
- [Anti-abuse reference](/docs/forms/anti-abuse)
