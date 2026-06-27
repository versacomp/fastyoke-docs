---
title: Status & Reliability — FastYoke
summary: How FastYoke stays up — current operating status, the controls that keep the platform running, and what happens when something breaks.
order: 6
---

::hero{eyebrow="Status" title="Status &amp; reliability" tagline="How we keep FastYoke running, where we post incidents, and how affected customers hear from us first. No vanity numbers — just the controls and the commitments behind them."}
::

::marketing-section{band="white" maxWidth="3xl"}

## Current status

::callout{type="note"}
**FastYoke is in early access.** We are onboarding customers in rolling cohorts ahead of general availability. There are no active incidents. When one occurs, we post it on this page **and** notify affected tenants directly — we don't wait for you to find it here.
::

As we approach general availability we'll publish a live operational dashboard with per-component status and uptime history. Until then, this page is the authoritative summary of how the platform is operated and how we'll communicate when something goes wrong.

::

::marketing-section{band="gray" maxWidth="3xl"}

## How we keep FastYoke running

Reliability is built into the architecture, not bolted on afterward.

- **Continuous backups.** Every database is backed up continuously to S3-compatible cold storage, so the recovery point is measured in seconds, not hours. Backup encryption inherits the volume encryption.
- **Live replication.** Tenant data is replicated across instances so a single node failing is not a single point of data loss.
- **Self-healing health checks.** Role-aware health checks keep traffic on healthy nodes and recover a wedged primary automatically, without waiting for a human to wake up.
- **Exercised recovery.** Restore procedures are documented and drilled — a backup you have never restored is a backup you do not have.
- **Encryption in transit.** All traffic is served over TLS. There is no plaintext path for tenant data over the network.
- **Region pinning (Enterprise).** Tenants with data-residency obligations can pin their data to a specific region.

::

::marketing-section{band="white" maxWidth="3xl"}

## When something breaks

- **We detect it.** Monitoring and health checks surface degradation before most users notice it.
- **We tell you.** Incidents are posted here and affected tenants are notified directly. We commit to clear, jargon-free updates with a real cause — not "a subset of users may have experienced issues."
- **We write it down.** Material incidents get a post-incident review so the same failure doesn't happen twice.
- **Security issues have a fast lane.** Report a vulnerability to [security@fastyoke.io](mailto:security@fastyoke.io) — we acknowledge within 24 hours. See the [Security](/security) page for the full posture.

::

::marketing-section{band="gray" maxWidth="3xl"}

## Stay informed

- See what we've shipped lately on the [Changelog](/changelog).
- Review the full security and isolation model on the [Security](/security) page.
- Questions for a procurement or security review? Email [security@fastyoke.io](mailto:security@fastyoke.io).

::cta-button{to="/getting-started" variant="primary" size="md"}
Request early access
::

::
