---
title: Sign-in flows
summary: The three sign-in paths today — local email-and-password, OTP step-up (SMS or email), and the WorkOS AuthKit hosted flow. How the platform picks between them, how to enroll a phone, and what to check when a sign-in doesn't work.
order: 2
---

# Sign-in flows

The platform supports three sign-in paths today. Most
tenants land on one; the OTP path layers on top of the
local path when 2FA is required.

## The three paths

### 1. Local email-and-password

`POST /auth/login` accepts an email and password and
returns a tenant JWT directly. This is the default for
tenants that haven't enabled SSO and don't require OTP.
The JWT shape and TTL are documented in
[the index](/docs/auth#1-tenant-user-jwt).

### 2. OTP step-up

When OTP is required (tenant-level or org-level toggle),
`POST /auth/login` does **not** return a JWT — it returns
an `otp_challenge` branch with a challenge id. The client
then:

1. Calls `POST /auth/otp/send` with the challenge id and
   a channel (`sms` or `email`).
2. The platform dispatches the OTP code.
3. Calls `POST /auth/otp/verify` with the challenge id
   and the code. On success the tenant JWT is returned.

### 3. WorkOS AuthKit hosted

When AuthKit is enabled for the account (an operator
action — see [SSO with WorkOS](/docs/auth/sso-with-workos)),
the local-password path is bypassed entirely. The browser
redirects to a WorkOS-hosted sign-in page; on success
`GET /auth/authkit/callback` lands the user back on the
platform with a tenant JWT minted.

## How the platform picks

The platform checks, in order:

1. Is AuthKit enabled on the tenant's account? If yes, the
   sign-in page redirects to WorkOS.
2. Is OTP required at the tenant or org level? If yes, the
   `/auth/login` response is an `otp_challenge` instead of
   a JWT.
3. Otherwise, `/auth/login` returns the tenant JWT
   directly.

## Enrolling a phone for SMS OTP

Before a user can complete SMS OTP, the platform needs a
verified phone number on their record. The enrollment
flow is a two-step OTP against the candidate number:

1. `POST /auth/otp/enroll-phone/send` with the candidate
   phone number.
2. The platform dispatches an OTP code by SMS.
3. `POST /auth/otp/enroll-phone/verify` with the
   submitted code. On success the phone is stored on the
   user record and is usable as an SMS OTP channel
   thereafter.

Tenant admins typically walk a user through this on first
sign-in after OTP is required.

## Troubleshooting

Three documented failure modes when sign-in doesn't work:

- **Email OTP landed in Spam (especially Yahoo).** The
  platform sends from `send.fastyoke.io`. Tell the user
  to mark a previous OTP email as Not Spam and allowlist
  the domain. Yahoo specifically is the most aggressive
  filterer.
- **Transient 503 on `/auth/login`, `/auth/otp/send`, or
  `/auth/otp/verify`.** This is a backplane issue, not a
  credentials problem. Retry once — the 503 typically
  clears in seconds. If it persists for more than a
  minute, check the platform status page.
- **"AuthKit is disabled for your account."** The
  operator hasn't enabled SSO for the account. The local
  + OTP paths still work in the meantime — for the
  enablement timeline, see
  [SSO with WorkOS](/docs/auth/sso-with-workos).

## What's not in sign-in today

- **Authenticator-app TOTP** as a replacement or
  alternative to email OTP.
- **WebAuthn passkeys.**
- **Social sign-in** (Google / Apple / GitHub) via
  WorkOS without a full AuthKit Organization mirror.

## See also

- [Authentication & Scopes](/docs/auth) — the token
  shapes a successful sign-in produces.
- [API tokens](/docs/auth/api-tokens) — the
  non-interactive credential shape for CI.
- [SSO with WorkOS](/docs/auth/sso-with-workos) — the
  AuthKit hosted flow in detail.
