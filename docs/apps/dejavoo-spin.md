---
title: Dejavoo SPIn Payments
summary: Take card-present payments on your Dejavoo terminals from the parts counter and service cashier.
order: 6
---

# Dejavoo SPIn Payments

Dejavoo SPIn Payments connects your own Dejavoo merchant account and card
terminals to FastYoke, so your counter and cashier can take a chip or
contactless card right at the terminal — the card never passes through FastYoke.

## What you'll need

- A **Dejavoo merchant account**.
- One or more **SPIn-enabled Dejavoo terminals**. Each terminal has a **TPN**
  (Terminal Profile Number) — find it on the device under the red **Settings**
  icon. A green arrow on the terminal means it is registered and online.
- Your SPIn **server URL** and **API key** (request these from Dejavoo).

## Connecting your account

1. Install **Dejavoo SPIn Payments** from the Marketplace.
2. Open **Connectors** under the Yoke section.
3. Enter your SPIn **server URL** and **API key** (and your integrator id, if you
   have one), then click **Connect Dejavoo**. We validate the credentials
   before saving them, and we store them encrypted.

## Registering a terminal

Add each terminal by its **TPN** and give it a label (for example, "Parts
counter" or "Service cashier"). You can register as many terminals as you run.
Removing a terminal stops it being offered when taking a payment.

## Taking payments

Taking a payment from a repair order or parts counter ticket arrives with the
**Auto Dealer Yoke**. There, your cashier picks a terminal, confirms the amount,
and the customer taps or inserts their card on the terminal; the approval (or
decline) returns to the screen with the masked card and authorization code.

## What's stored where

Your card processing happens on the Dejavoo terminal and your merchant account.
FastYoke stores only the **masked** result of each transaction — the last four
digits, card type, authorization code, and reference — never a full card number.
