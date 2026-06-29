---
title: FastYoke Substrate — native mobile apps for your employee fleet
summary: FastYoke Substrate is the native mobile delivery layer for deploying internal apps to employee devices without App Store review. Build in the App Builder, compile to Android and iOS, and distribute privately via MDM or install link. Team+ tier. Early access Q1 2027.
order: 8
---

::hero{eyebrow="Team+ · Mobile · Private Distribution · Early access Q1 2027" title="Your internal apps on your employees' devices. No App Store required." tagline="FastYoke Substrate is the native mobile delivery layer built into the FastYoke platform. Build in the App Builder, compile to native Android and iOS, and distribute directly to your employee fleet — signed, OTA-updatable, with no app review cycle, no Expo EAS, and no third-party MBaaS in the data path."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

::maturity-badge{status="early-access" label="Q1 2027"}

## The one-sentence posture

Internal tools don't belong in the App Store. The review cycle, the
enterprise developer program enrollment, the public app identity —
none of that is appropriate for an app that dispatches your field
technicians or routes your warehouse floor. Substrate is FastYoke's
answer: native Android and iOS delivery that never touches a public
catalog, served from the same platform your workflows already run on.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
What ships

#title
1. Four capabilities in one delivery layer

#lede
Substrate is built into the FastYoke platform at the Team+ tier. No separate SDK to install, no third-party MBaaS account. Build in the App Builder, distribute from the same dashboard.

#default
<div id="what-ships"></div>

::feature-grid{columns="2"}
  ::feature-card{title="No-Code → Native" icon="crm" accent="sky" summary="Apps built in the FastYoke App Builder compile to native Android (.aab/.apk) and iOS (.ipa). No Expo EAS account, no mobile toolchain to manage — builds run on FastYoke's off-box runner pool and land in your private distribution channel."}
  ::
  ::feature-card{title="Self-hosted OTA updates" icon="logistics" accent="cyan" summary="Push a JS bundle update and every enrolled device picks it up silently on next launch. No App Store review round-trip. No version fragmentation across your fleet. Bundles are content-addressed, Ed25519-signed, and scoped to your tenant."}
  ::
  ::feature-card{title="Private distribution" icon="warehouse" accent="violet" summary="Deliver via MDM (Intune, Jamf, any EMM that accepts an IPA or APK) or a token-gated install link. Your app never appears in a public catalog. No Apple Developer Enterprise Program required — MDM distribution uses your Apple Business Manager enrollment, not the Enterprise Program."}
  ::
  ::feature-card{title="On-Prem compatible" icon="field-service" accent="amber" summary="Substrate works with FastYoke On-Prem. OTA bundles are served from your own hardware. PHI, operational data, and build artifacts never leave your network — the air-gap path for regulated deployments."}
  ::
::

::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Who needs it

#title
2. Four deployment profiles

#lede
If any of these describes your situation, the App Store is the wrong distribution path — regardless of what an enterprise developer program enrollment would buy you.

#default
<div id="profiles"></div>

::feature-grid{columns="2"}
  ::feature-card{title="Healthcare — clinical staff apps" icon="crm" accent="rose" summary="Dispatch patient intake, care coordination, and triage apps to nurses and technicians. PHI stays inside the hospital network — On-Prem + Substrate is the air-gap path for HIPAA-regulated deployments."}
  ::
  ::feature-card{title="Field service & logistics" icon="logistics" accent="sky" summary="Dispatch, inspection, and proof-of-delivery apps on the phones your drivers and technicians already carry. OTA updates push route changes and form versions without a manual update prompt."}
  ::
  ::feature-card{title="Financial services" icon="accounting" accent="emerald" summary="Compliance checklists, audit capture, and advisor tools on branch staff devices. No consumer App Store identity means no public footprint for internal tooling."}
  ::
  ::feature-card{title="Critical infrastructure" icon="warehouse" accent="cyan" summary="Operational control and inspection apps inside the OT/IT boundary. Air-gap compatible with On-Prem — the device fleet updates from your own hardware, not a cloud endpoint."}
  ::
::

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Pricing

#title
3. Device fleet add-on (Team+)

#default

Substrate is available at the Team tier and above as a monthly device
fleet add-on. OTA updates are unlimited within the fleet tier; native
builds are metered per build.

| Fleet tier | Devices | OTA updates | Android build | iOS build | Monthly |
|---|---|---|---|---|---|
| **Fleet 100** | up to 100 | Unlimited | $1.50 | $7.00 | **$49** |
| **Fleet 500** | up to 500 | Unlimited | $1.50 | $7.00 | **$179** |
| **Fleet Enterprise** | 500+ | Unlimited | Custom | Custom | **Custom** |

Device overage above the fleet cap: $0.35/device/month.
iOS builds run on a bare-metal macOS runner pool — async, 8–15 minutes
per build. Android builds run on Linux Docker — 3–8 minutes.

Team+ base subscription required for all fleet tiers.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
4. What Substrate isn't for

#default
- **Early access, Q1 2027.** Substrate is in active development. The
  OTA gateway and private distribution channels are shipping
  sub-feature by sub-feature now; GA is targeted for Q1 2027.
  Early-access conversations can start today.
- **Team+ required.** Not available on Solo, Starter, or Pro tiers.
- **Build times are async, not instant.** Android builds complete in
  3–8 minutes on the Linux Docker runner pool. iOS in 8–15 minutes on
  bare-metal macOS. Substrate queues and notifies — it is not a local
  `xcodebuild`.
- **Substrate handles the binary; your MDM handles the policy.**
  Device enrollment, certificate management, and compliance profiles
  stay with Intune/Jamf/your EMM. Substrate delivers the app; it does
  not replace your MDM.
- **Private distribution only in v1.** Consumer App Store submission
  (App Store Connect, Google Play) is out of scope for the initial
  release. Substrate is purpose-built for internal fleet distribution.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Start the early-access conversation

Substrate early access is available to Team+ customers ahead of the
Q1 2027 general release. Email
[security@fastyoke.io](mailto:security@fastyoke.io) with the subject
*FastYoke Substrate* and we'll route you to the right conversation.

::cta-button{to="mailto:security@fastyoke.io?subject=FastYoke%20Substrate" variant="primary" size="lg"}
Start the early-access conversation
::

::cta-button{to="/on-prem" variant="secondary" size="lg"}
See compatible deployment shapes — On-Prem
::

For ISVs building on FastYoke locally before embedding inside a client
network, the [FastYoke Runtime](/runtime) page covers the development
workflow.

::
