---
title: EHR / ADT Connector
description: Keep your Patient Flow records live from your hospital's HL7v2 ADT feed.
---

# EHR / ADT Connector

The EHR / ADT Connector ingests **HL7v2 ADT** (admit / transfer / discharge)
messages from your hospital's interface engine and keeps the Patient Flow app's
patient, bed, and unit records up to date automatically — read-only, one
direction (we never write back to your chart).

## How it works

Your interface engine posts ADT messages to a secure, per-tenant endpoint over
HTTPS; we acknowledge each message with a standard HL7 ACK. Admit, transfer, and
discharge events update the matching patient encounter and bed in Patient Flow.

## Data protection

Patient identifiers are encrypted at rest. The connector is available to
Enterprise customers under a Business Associate Agreement; your administrator
enables it for your organization once that agreement is in place.

## Enabling

Ask your administrator to enable the connector for your organization. Once
enabled, open **Connectors**, generate the inbound endpoint token, and share the
endpoint URL + token with your integration team to configure your interface
engine.
