# Sign up

> Create a FastYoke tenant in under a minute — enter your email, confirm it, you're on the free Hobby tier.

# Sign up

Go to **`/signup`** on your FastYoke deployment, enter an email and
password (8 characters minimum), pass the bot challenge, and you're
signed in. No sales call, no invite needed.

On success you land on `/admin` with a **Hobby** tenant already
provisioned — your workspace is named after the local part of your
email address.

## Email verification

Account access is immediate. A verification email goes out in the
background. Until you click the link, an in-app banner sits at the
top of the admin shell with a **Resend** button and a dismiss option.
Unverified accounts are otherwise fully functional.

## What Hobby includes

A working tenant with every surface other tiers get — just sized for
personal projects and small teams. Full capability matrix at
[Forms tier entitlements](/docs/forms/tiers).

Highlights:

- 3 active (published) forms.
- 100 submissions per day across all forms.
- Cloudflare Turnstile challenge on every public form (always-on at
  this tier).
- The "Powered by FastYoke" badge on every public surface — Pro and
  above can opt it out from **Branding** settings.
- Submission PDFs via the built-in Typst renderer (no custom
  template).

## Upgrade to Pro or Team

Two paths:

**At signup** — append `?upgrade=pro` or `?upgrade=team` to the
signup URL before submitting. After your account is created, the
browser redirects straight to Stripe Checkout. A successful charge
sets the tier; you land on `/admin` already upgraded.

If Stripe is not configured on the deployment, you'll see an inline
notice and a "Continue to dashboard" link — your account is still
created at the Hobby tier and you can upgrade later.

**After signup** — go to **Settings → Billing → Upgrade**, pick a
plan, and complete the card checkout. Tier changes propagate within
~30 seconds of a successful charge. No downtime — submissions in
flight at the moment of upgrade are counted against the old tier's
caps; every submission from the next one onward counts against the
new tier.

Downgrades follow the reverse path. Existing submissions and forms
are preserved; new submissions count against the lower tier's caps.

> **Duplicate email**
>
> If you already have an account, the signup form shows "An account
>   with that email already exists." Sign in from `/login` instead, or
>   use **Forgot password** to reset your credentials.
