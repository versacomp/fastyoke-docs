---
title: SMS Opt-In — FastYoke
summary: How FastYoke users opt in to receive transactional SMS messages, with screenshots of the in-product consent screen and the seven required disclosures.
order: 5
---

::hero{eyebrow="SMS" title="SMS opt-in workflow" tagline="How FastYoke users consent to receive transactional SMS messages, with screenshots of the in-product consent screen and all required disclosures."}
::

::marketing-section{band="white" maxWidth="3xl"}

**Last updated:** 2026-05-26.

This page documents the opt-in workflow for **FastYoke** SMS
messages. SMS opt-in happens inside an authenticated FastYoke
account; this public page exists so anyone — including carrier
and registry reviewers — can verify the workflow and the
disclosures the user sees before consenting.

## Program (brand) name

**FastYoke** — operated by FastYoke (www.fastyoke.io), a
multi-tenant logistics SaaS platform.

## Who receives SMS

Authenticated FastYoke account holders who have voluntarily
added a mobile phone number to their account and completed a
two-step verification of that number. SMS is **not** sent to
anyone who has not completed this opt-in.

## What recipients receive

Transactional SMS only — no promotional, marketing, or
informational broadcast messages. Specifically:

- One-time passcodes for login and two-factor authentication
- Account verification and password-reset confirmation codes
- Security alerts for new device sign-ins
- (For tenant operators that have configured it) job
  lifecycle notifications tied to shipments the recipient is
  associated with

## The opt-in workflow, step by step

Opt-in is a **two-step confirmation**: the user enters a
number, then proves they control it by entering a code FastYoke
sends to that number. No SMS is sent after step 1 alone, and no
ongoing SMS is sent if step 2 is not completed.

### Step 1 — User navigates to Two-Factor Authentication settings

After signing in, the user opens **Account → Security →
Two-Factor Authentication** from the admin console.

![Screenshot: navigation path from the user menu to Account → Security → Two-Factor Authentication](/sms-opt-in/01-navigation.png)

### Step 2 — User selects "SMS" as the 2FA method

The user is presented with available 2FA methods. Selecting
**SMS** reveals the phone-number entry field and the full
consent disclosure (reproduced below in
[Disclosures](#disclosures)).

![Screenshot: SMS method selected, consent disclosure visible above the phone-number field](/sms-opt-in/02-consent-screen.png)

### Step 3 — User enters their phone number and clicks "Send verification code"

By clicking **Send verification code**, the user actively
consents to the disclosures shown on the screen. FastYoke
sends a single verification SMS to the entered number.

![Screenshot: phone-number field filled in and "Send verification code" button highlighted](/sms-opt-in/03-send-code.png)

### Step 4 — User enters the verification code

The user enters the code they received via SMS into the
verification field and clicks **Verify**. Only after this step
is the number stored and eligible to receive future SMS.

![Screenshot: verification-code entry field and "Verify" button](/sms-opt-in/04-verify-code.png)

### Step 5 — Confirmation

The user sees a confirmation that SMS 2FA is now active for
their account, along with the **STOP** / **HELP** keyword
reminder and a link to remove the number at any time.

![Screenshot: confirmation screen showing SMS 2FA active, STOP/HELP reminder, and remove-number control](/sms-opt-in/05-confirmation.png)

## Disclosures

The following seven disclosures are presented on the consent
screen (Step 2 above) **before** the user enters their phone
number. They are reproduced here verbatim:

1. **Program (brand) name.** "FastYoke will send you a
   one-time passcode via SMS to verify this number and, going
   forward, for login and security-sensitive actions on your
   FastYoke account."

2. **Message frequency.** "Message frequency is event-driven —
   you will receive an SMS only when you sign in, reset your
   password, or trigger a security-sensitive action. Typically
   fewer than 10 messages per month per user."

3. **'Message and data rates may apply' disclosure.**
   "Standard message and data rates may apply depending on
   your mobile carrier and plan."

4. **Opt-out information.** "Reply **STOP** to any FastYoke
   SMS to opt out. You can also remove your phone number from
   **Account → Security → Two-Factor Authentication** at any
   time. Opting out of SMS does not close your account."

5. **Customer care contact information.** "Reply **HELP** for
   help, or email
   [support@fastyoke.io](mailto:support@fastyoke.io)."

6. **Terms & conditions link.** "[Terms of
   Service](https://www.fastyoke.io/terms)" — a publicly
   accessible page that includes the SMS terms in §5.

7. **Privacy policy link.** "[Privacy
   Notice](https://www.fastyoke.io/privacy)" — a publicly
   accessible page that describes how FastYoke handles your
   phone number and other personal data.

## How to opt out

A recipient can opt out at any time, by any of:

- Replying **STOP** to any FastYoke SMS — opt-out takes
  effect immediately and a single confirmation SMS is sent.
- Removing the phone number from **Account → Security →
  Two-Factor Authentication** in the admin console.
- Emailing
  [support@fastyoke.io](mailto:support@fastyoke.io) and
  asking to be removed.

Opting out of SMS does not close the account or affect
non-SMS notifications (e.g., email).

## Customer care

- **Email:** [support@fastyoke.io](mailto:support@fastyoke.io)
- **Reply HELP** to any FastYoke SMS for an automatic help
  response with the support email address.

## Supporting public URLs

- **Terms of Service:**
  [https://www.fastyoke.io/terms](https://www.fastyoke.io/terms)
  (SMS-specific terms in §5)
- **Privacy Notice:**
  [https://www.fastyoke.io/privacy](https://www.fastyoke.io/privacy)
- **This page (opt-in workflow + screenshots):**
  [https://www.fastyoke.io/sms-opt-in](https://www.fastyoke.io/sms-opt-in)

::
