---
name: app-store-review-bot
description: Audits an iOS app's metadata, screenshots, subscription design, privacy declarations, and reviewer notes against the App Store Review Guidelines before submission, and guides the response when a rejection arrives. Use before submitting or resubmitting to App Store Connect, when choosing an app name or keywords, when designing an auto-renewing subscription, when a rejection cites a guideline number, or when the user mentions App Review, Resolution Center, an appeal, or a trademark complaint.
---

# App Store Review Bot

A pre-submission audit and rejection-response playbook for iOS apps, built from patterns that
recur across real App Review rejections and appeals.

The guidelines themselves are published by Apple. What this skill adds is the operational
layer: which guidelines fire on which app shapes, what the canned rejection text actually
means, what fix clears it, and which rejections are worth arguing with.

Companion files:

- [APPEALS.md](APPEALS.md) — when to appeal, what wins, letter and phone-call playbooks
- [TEMPLATES.md](TEMPLATES.md) — reviewer notes, paywall disclosure checklist, questionnaire answers

## Source of truth

Guidelines: <https://developer.apple.com/app-store/review/guidelines/>

**This file is current through Apple's June 8, 2026 update.** No copy of the guidelines is
bundled here on purpose — a snapshot starts contradicting Apple within months, and Apple's text
is theirs, not redistributable under this repository's license.

Fetch the live page when running a full pre-submission audit, when a rejection cites a guideline
number, or when the date above looks stale. Check the "Last Updated" line at the bottom of that
page. If it is newer than the date above, treat the live text as authoritative and say plainly
which entries below may have drifted.

Offline, or without a fetch tool: the quoted reviewer language below stays accurate, but
guideline **numbers move**. Apple has renumbered at least twice within the span this file
covers — see the historical notes on 3.1.3(e) and 2.3.8. Describe the rule, and verify the
number before quoting it to a reviewer.

Two process URLs worth having at hand:

- Contact App Review, and the appeal entry point: <https://developer.apple.com/contact/app-store/>
- Report infringement of your own IP, per 5.2: the claim web form linked from that page

## Four facts that shape everything else

**1. The rejection email contains no reason.** Apple's notification is a pointer to
Resolution Center. Even a subject line reading "does not comply with the App Store Review
Guidelines" is a stub. Never wait for an email to explain a rejection, and never conclude no
reason was given.

**2. Reviewer text is not archived anywhere.** Resolution Center threads are reachable only
while the submission sits in Rejected or Unresolved Issues. Once the version is approved or
replaced, the thread is gone. It is not in the App Store Connect UI, not in the public API,
and not in the internal API behind the web app. Copy every rejection message into your own
notes the day it arrives, or lose it permanently.

**3. Rejections routinely cite things the app already does.** Missing disclaimers that are
present, missing account deletion that exists, demo credentials that work, features that were
never in the binary. Treat some fraction of rejections as a default filter to disprove rather
than a finding about your app. The countermeasure is not to comply harder in code — it is to
make existing compliance impossible to miss, and to state it in the reviewer notes with exact
tap paths.

**4. A reviewer's verbal approval does not bind the next reviewer.** A design blessed on a
phone call can be rejected on the next submission by someone else, with no acknowledgment that
the conversation happened. Get concessions in writing in Resolution Center, and even then
treat them as non-binding.

## Sequencing: what to ship in the first submission

**A first submission is reviewed far more harshly than any update to the same app.** The
initial review establishes the app; updates are reviewed against an app Apple has already
accepted. The same feature that draws questions on submission one often passes without comment
as an update three weeks later.

Two rules follow.

**Ship the core, and only the core, in the initial review.** Cut anything that is not required
to demonstrate the app's central purpose. Every additional surface is another thing to be
misunderstood, another feature claim in the description to be hunted for, another permission to
justify, another screen to be called crowded. Get the smallest coherent version of the app
approved, then add breadth through updates.

**Do not submit in-app purchases with version 1.0.** Ship the first version free with no IAP at
all, get it approved, then add purchases in an update.

Why this one matters more than it looks: in-app purchases are reviewed as separate items inside
the same submission, so one rejected purchase puts the whole submission into Unresolved Issues.
A paywall argument you would have had eventually now blocks the app from existing at all, and
every round trip re-reviews the binary alongside the purchase. Split it and the app clears on
its own merits, an IAP rejection costs a feature ship rather than the launch, and the arguments
about recurring value, free tiers, and disclosure text are far easier to make about a live app
with real usage than about a binary the reviewer has never seen work.

**The tension to manage.** Shipping narrow runs straight into 4.2 minimum functionality, and
into 4.3 spam if the app resembles others on the account. Narrow is not the same as thin: the
first version must do one thing completely and better than the obvious alternative, not do
several things partially. If cutting to the core leaves something that reads as a utility
wrapper or a single feed, you have cut past the core and the launch will fail on 4.2 instead.
Write the competitor feature comparison before deciding what to cut, and keep whatever makes
that comparison win.

## Guideline hit list

Grouped by what triggers them. Quoted text is Apple's canned reviewer language, which repeats
near-verbatim across rejections.

Three evidence levels:

- **Unmarked** — observed rejection: canned reviewer language and first-hand behavior.
- ***(guideline text; no rejection in this record)*** — full operational entry drawn from the
  published guidelines, without verbatim rejection text from this record. All five guideline
  sections (1.x–5.x) use this level for completed answers where no rejection was on file.
- ***(coverage stub)*** — thin placeholder for numbers not yet given a full entry. Not used
  while coverage is complete. Fetch the live guidelines page before quoting Apple's wording.

### Completeness and distribution

**2.1 App Completeness, demo account** — "We were unable to sign in to review your app with
the demo account information you provided."

This arrives even when the credentials work. For anything credential-gated, attach a screen
recording of a successful login on the first submission, not the second.

Two provisions in the current text worth using. If legal or security obligations prevent you
from handing over a working account, a **built-in demo mode is permitted in place of a demo
account, with prior approval from Apple** — the demo mode must exhibit the app's full features
and functionality. And per 2.1(b), configured in-app purchases must be complete, visible, and
functional for the reviewer; if any cannot be reached in the app, the reason has to be explained
in the review notes.

Also remember the backend counts as part of completeness. A staging service that is down, or a
feed that is empty during review, reads as a broken app.

From 2.1(a): submissions, including pre-order binaries, must be final — scrub placeholder text,
empty websites, and temporary content; test on-device for crashes before you submit. Incomplete
bundles and obvious technical failures reject here, not under Design.

**2.1 Information Needed** — a single ambiguous word in the description can trigger a request
to define it. Read the description as a hostile stranger and flag any term that invites the
question "what is that?" Expect follow-up 2.1 rounds on unrelated topics, including the
pricing model, with nothing having changed in the app between them.

HealthKit and CareKit submissions draw a standard Clinical Health Records questionnaire: how
the app integrates with the API, where data is uploaded and how often, which features require
the data, how the feature is enabled and disabled, how else the data is used, where it is
stored, who has access. Answer every part in one reply — partial answers restart the loop. A
worked answer is in [TEMPLATES.md](TEMPLATES.md).

**2.2 Beta Testing** *(guideline text; no rejection in this record)* — demos, betas, and trial
versions do not belong on the App Store. Use TestFlight. Any TestFlight build must still be
intended for eventual public distribution and must comply with the App Review Guidelines.
Testers cannot be paid or compensated for using TestFlight, including as a crowdfunding reward.
Significant updates to a beta must go through TestFlight App Review before you distribute them
to testers.

What fails 2.2 on the store: "beta" in the name or screenshots, watermarked unfinished UI,
feature flags that leave half the app dead, and copy that tells users they are in a trial build.
What belongs on TestFlight stays on TestFlight until it is a finished product.

Clear path: ship a complete 1.0 (see Sequencing), move unfinished cohorts to TestFlight, strip
beta language from metadata, and never pay testers for installs. If Review cites 2.2, either
finish the product or pull the build and resume on TestFlight — do not argue that customers
"know it is early access."

### Metadata and store presence

**2.3 Accurate Metadata** — "We were unable to locate some of the features described in your
metadata," followed by your own description bullets quoted back.

Every marketing bullet in the description, release notes, and screenshots is a feature claim
the reviewer will hunt for. Aspirational copy, coming-soon features, and anything behind a
data feed that happened to be empty during review all read as missing. Either make each bullet
reachable in a few taps from launch, or delete the bullet. Also confirm the copy describes the
version being submitted, not a later build. Privacy information, description, screenshots, and
previews must stay aligned with the core experience across versions.

**2.3.1 Hidden features** — "We noticed that your app may contain hidden features,
functionality, or content," plus a specific accusation, then: "Attempting to hide features,
functionality, or content in your app may lead to removal from the Apple Developer Program."

This one fires as a false positive in regulated-adjacent categories — betting, gambling,
alcohol, health, finance, anything money-adjacent. Two penalties attach automatically whether
or not the finding is correct: longer review on the next submission, and loss of expedited
review eligibility until resolved.

Audit for remote config, feature flags, server-driven UI, and third-party SDKs, which count as
your code for this guideline. If a flag must exist, default it to the reviewed state and say so
in the notes. Pre-empt with reviewer notes stating plainly what the app does not do.

The current guideline makes detailed reviewer notes a requirement rather than a tactic. Its
words: all new features, functionality, and product changes "must be described with specificity
in the Notes for Review section of App Store Connect (generic descriptions will be rejected) and
accessible for review." So "bug fixes and improvements" in the notes is itself a 2.3.1 exposure.
2.3.12 says the same about "What's New" text: only genuine bug fixes, security updates, and
performance work may use a generic description.

Same guideline also covers misleading marketing of the app anywhere, inside or outside the App
Store, including promoting a false price — grounds for removal and account termination.

Pushing back on a factually wrong 2.3.1 can draw an escalation citing Section 3.2(f) of the
Developer Program License Agreement — a termination warning — with no specifics attached. That
is a signal to stop replying and appeal. See [APPEALS.md](APPEALS.md).

**2.3.2 In-app purchase disclosure in metadata** *(guideline text; no rejection in this
record)* — if the app includes IAP, the description, screenshots, and previews must make clear
when featured items, levels, subscriptions, or similar require additional purchases. Promoting
IAP on the App Store requires public-appropriate Display Name, Screenshot, and Description,
compliance with Apple's Promoting Your In-App Purchases guidance, and correct handling of
`SKPaymentTransactionObserver` so a promoted purchase can finish when the app launches.

Failure mode: hero screenshots of a locked level with no "In-App Purchase" cue, or a description
that reads fully featured while the binary paywalls the core loop. Fix by labeling purchase
gates in metadata, aligning promoted IAP creatives with a 4+ public audience where required by
2.3.8, and testing the promoted-IAP launch path end to end before submit.

**2.3.3 / 2.3.4 Screenshots and previews** — "We noticed that your screenshots do not
sufficiently reflect your app in use."

Screenshots must show the app running, not a marketing collage. Also cited for wrong device
frames and for captions. App previews get rejected for horizontal orientation and for
including a device frame. Previews may only use video screen captures of the app itself
(Stickers and iMessage extensions may show the Messages experience). Narration and overlays are
allowed to clarify; title-art-only or splash-only sets are not.

**2.3.5 Category selection** *(guideline text; no rejection in this record)* — pick the most
appropriate category using Apple's App Store Category Definitions. If you are way off base,
Apple may change the category for you — which can wreck ASO you thought you owned.

Audit: primary and secondary categories against what the first session actually does, not what
marketing wishes the app were. A thin utility in Games, or a game in Productivity, invites a
silent category move or a metadata rejection adjacent to 4.2. Fix by matching category to the
core loop before you submit, and revisit after large feature shifts.

**2.3.6 Age rating honesty** *(guideline text; no rejection in this record)* — answer App Store
Connect age rating questions honestly so parental controls work. A mis-rated app surprises
customers and can draw regulator attention. If the app includes media that requires content
ratings or warnings in a territory (films, music, games, and similar), you must meet local
requirements wherever the app is sold.

Treat the questionnaire as evidence, not a marketing dial. Underrating violence, gambling,
or unrestricted web access to chase a lower badge is a 2.3.6 problem and often a later Safety
problem. Fix: re-answer truthfully, update screenshots to match the real rating, and document
territory-specific warnings when you carry rated media.

**2.3.7 Metadata, name and keywords** — the guideline cited when a third-party trademark
appears in the name, description, or listing. Names max out at 30 characters. Do not pack
metadata with trademarked terms, popular app names, pricing, or irrelevant phrases. Names,
subtitles, screenshots, and previews should not carry prices, terms, or descriptions that do
not belong to that metadata type. Subtitles must not reference other apps or make unverifiable
claims. Apple may rewrite abusive keywords without asking.

**2.3.8 Metadata appropriate for all audiences** — every icon, screenshot, and preview must
hold to a 4+ age rating even when the app itself is rated higher. "For Kids" and "For Children"
are reserved to the Kids Category and may not appear in the name, subtitle, icon, screenshots,
or description of an app outside it.

The same guideline carries a name-consistency clause: metadata including app name and icons
"should be similar to avoid creating confusion," covering small, large, Watch, and alternate
icons. Rejections here can reference an on-device name that appears nowhere in the current
build, so check the display name per build configuration and per locale.

Historical note: for years this number was cited primarily for name mismatch across locales.
The current text leads with age-appropriate metadata.

**2.3.9 Rights and fictional account data in creatives** *(guideline text; no rejection in this
record)* — you must hold the rights to everything in icons, screenshots, and previews, and you
should show fictional account information instead of a real person's data.

Real emails, phone numbers, faces, chats, and medical charts in screenshots are a privacy and
rights landmine. Stolen art and unlicensed celebrity likeness fail here and under 5.2. Fix:
replace production PII with clearly fake sample data, license or create every creative asset,
and keep a rights folder ready if Legal asks. Pair with 5.1 when the screenshot exposes someone
else's personal data.

**2.3.10 Apple-platform focus in metadata** *(guideline text; no rejection in this record)* —
metadata and the app experience should focus on the Apple platforms you support. Do not include
names, icons, or imagery of other mobile platforms or alternative app marketplaces in the app
or metadata unless there is specific, approved interactive functionality. Drop irrelevant
cross-platform marketing filler.

Classic miss: Android robot in screenshots, "also on Google Play" banners, or competing store
badges in the description. Fix: Apple-only creatives, remove foreign-store chrome, and only
mention another platform when the binary actually offers approved interactive functionality that
requires it.

**2.3.11 Pre-order completeness** *(guideline text; no rejection in this record)* — pre-order
apps must be complete and deliverable as submitted. The app you eventually release must not be
materially different from what you advertised during pre-order. Material changes (including
business model changes) require restarting pre-order sales.

Do not use pre-order as a placeholder reservation while the product is still vapor. If the
model, core features, or promised content change substantially, restart pre-order rather than
hoping nobody notices. Notes should explain any non-material polish differences.

**2.3.12 What's New accuracy** *(guideline text; no rejection in this record)* — "What's New"
must clearly describe new features and product changes. Generic text is allowed only for simple
bug fixes, security updates, and performance improvements. Significant changes must be listed.

This pairs with 2.3.1 reviewer-notes specificity. "Bug fixes and improvements" on a feature
release is a dual exposure. Fix: bullet the real changes in What's New and mirror them in Notes
for Review with tap paths.

**2.3.13 In-app events** *(guideline text; no rejection in this record)* — in-app events are
timely events inside the app. To feature on the App Store, the event must use an event type
from App Store Connect. Event metadata must be accurate and about the event, not a second app
pitch. Events must occur at the dates and times you set, including across storefronts. Monetize
only under Section 3 Business rules. Deep links must land on the correct in-app destination.

Failure modes: evergreen marketing dressed as an "event," wrong deep links, times that do not
match reality across locales, and creatives that sell the whole app instead of the event. Fix:
pick a real event type, align schedules, test the deep link cold, and keep copy event-specific.
Read Apple's In-App Events guide before the first featured event.

**Extra keyword surface via a second localization.** Adding a Spanish (Mexico) localization and
writing its description and keyword field in English gets that text indexed in the US store as
well, since US search reads both the English and Spanish (Mexico) locales. It roughly doubles
the keyword field available for US ranking without adding an app or a name variant, and it does
not draw a rejection on its own.

Two constraints on the trick. The trademark rules apply per locale, so a brand name that is
disallowed in your primary keywords is equally disallowed here — a second localization is
extra surface, not a loophole. And every feature claim in the added description is still a
2.3 claim a reviewer can go hunting for, so keep it to keywords and accurate copy rather than
a second, looser pitch.

### Hardware compatibility

**2.4 Hardware Compatibility** *(guideline text; no rejection in this record)* — parent for
device behavior, power, peripherals, and platform-specific hardware rules. Most citations name
a 2.4.x child; map the body before you change architecture.

**2.4.1 Hardware compatibility** — an iPhone-only app can be rejected because a label
overlapped a control when run at 2x on iPad.

This is not an edge case. Reviewers frequently test iPhone-only apps on an iPad in 2x
compatibility mode, so 2x is effectively a required test configuration whether or not you
support iPad. Walk every screen there before submitting, and watch for the failures specific to
that mode: labels overlapping controls, text clipped by scaled-up frames, hardcoded frames that
assumed iPhone bounds, and touch targets drifting off their backgrounds. Apple also encourages
designing so customers can use the app across their devices when possible.

**2.4.2 Power, heat, and device strain** *(guideline text; no rejection in this record)* —
design for efficient power use. Apps must not rapidly drain battery, generate excessive heat, or
put unnecessary strain on device resources. Examples called out: encouraging the user to place
a charging device under a mattress or pillow, excessive write cycles to solid-state storage,
and running unrelated background work such as cryptocurrency mining (including via third-party
ads).

Audit loops that hammer the GPU with the screen off, unbounded logging to disk, background
fetch that does real work forever, and any mining or "earn while idle" crypto framing. Fix:
cap background work to declared modes, stop writing spam to disk, remove mining, and never coach
unsafe charging physical setups.

**2.4.3 Apple TV inputs** *(guideline text; no rejection in this record)* — tvOS apps should be
usable with the Siri Remote or third-party game controllers; enhanced behavior with other
peripherals is fine. If a game controller is required, say so clearly in metadata so customers
know they need extra hardware.

Failure: forcing a phone companion or unsupported HID with no remote path and no metadata
warning. Fix: complete remote navigation, declare required controllers in the listing, and test
with the Siri Remote only before submit.

**2.4.4 No restart or unrelated settings coaching** *(guideline text; no rejection in this
record)* — apps must never suggest or require a device restart, or modifications to system
settings unrelated to core functionality — for example telling users to turn off Wi-Fi or
disable security features.

Troubleshoot inside the app. If a setting is truly required (Local Network, VPN configuration
for a VPN product), explain the relevant setting only, never "turn off Find My" style advice.
Fix by deleting the coaching screen and replacing it with in-app recovery.

**2.4.5 Mac App Store hardware and packaging rules** *(guideline text; no rejection in this
record)* — Mac App Store distribution adds sandbox and packaging requirements this skill's iOS
focus rarely hits, but the numbers still matter when you ship a Mac binary: appropriate
sandboxing and File System rules; Xcode packaging only (no third-party installers); self-contained
single-app bundles; no auto-launch or leftover processes without consent; no shared-location
installs; no downloading standalone apps/kexts/code that changes reviewed functionality; no root
escalation or setuid; no custom license screens, license keys, or home-grown copy protection;
updates only via the Mac App Store; run on the current OS without deprecated stacks such as
Java; all localizations in one bundle.

If you are iOS-only, skip this in the audit. If you ship Mac, treat each Romanette as its own
checklist row before submit.

### Software requirements

**2.5 Software Requirements** *(guideline text; no rejection in this record)* — parent for public
API use, process behavior, networking, and platform-feature misuse. Prefer the specific 2.5.x
number in notes and responses.

**2.5.1 Software Requirements** — "Your app uses the HealthKit or CareKit APIs but does not
indicate integration with the Health app in your app description." The App Store description
must name the Health app integration, and say it is optional if it is.

The broader rule behind that rejection: public APIs only, running on the currently shipping OS,
used for their intended purpose, with the integration named in the description. HomeKit must do
home automation; HealthKit must do health and fitness and integrate with the Health app. Using a
framework for an adjacent purpose is a rejection even when it works. Phase out deprecated
technologies before they strand you on an old OS.

**2.5.2 Self-contained bundle, no remote code** *(guideline text; no rejection in this record)* —
apps may not read or write outside their container, and may not "download, install, or execute
code which introduces or changes features or functionality of the app." The only exception is
educational apps teaching or testing code, which must make the code fully viewable and editable
by the user and may not use it for anything else.

This is the guideline behind most 2.3.1 hidden-feature findings, and it is what makes
server-driven UI, remote feature flags that alter reviewed behavior, and JavaScript bundle
hot-updates dangerous rather than merely risky. Configuration values are fine; shipping new
behavior after review is not.

**2.5.3 Malware and disruption** *(guideline text; no rejection in this record)* — apps that
transmit viruses, files, code, or programs that harm or disrupt normal OS or hardware operation,
including Push Notifications and Game Center, are rejected. Egregious or repeated behavior means
Developer Program removal.

Do not ship "cleanup," crack, or exploit tooling. If a dependency is compromised, pull it before
review. This is account-level territory — respond with removal and forensics, not debate.

**2.5.4 Background modes for intended purposes** *(guideline text; no rejection in this
record)* — multitasking may only use background services for their intended purposes: VoIP,
audio playback, location, task completion, local notifications, and similar declared modes.

Declaring `audio` to keep a socket alive for non-audio work, or `location` without a real
location product, fails here and often under 5.1.5. Fix: enable only the modes you use, exercise
them in review notes with a demo path, and remove freeloading background modes.

**2.5.5 IPv6-only networks** *(guideline text; no rejection in this record)* — the app must be
fully functional on IPv6-only networks.

Test on an IPv6-only NAT64 network before submit. Hard-coded IPv4 literals and IPv4-only API
hosts fail in review environments. Fix DNS and dual-stack backends; do not tell the reviewer to
"use IPv4."

**2.5.6 WebKit for in-app browsing** *(guideline text; no rejection in this record)* — apps that
browse the web must use the appropriate WebKit framework and WebKit JavaScript. Alternative
browser engines require Apple's entitlement (documented for specific regions such as the EU and
Japan).

Embedded Chromium or custom engines without the entitlement reject. Fix: `WKWebView` / WebKit
stack, or obtain the alternative-engine entitlement where eligible and document it in notes.

**2.5.7 Intentionally omitted** *(guideline text; no rejection in this record)* — Apple marks
2.5.7 as intentionally omitted. There is no active requirement under this number. If an old
blog or rejection cites it, fetch the live guidelines and treat the citation as stale.

**2.5.8 Alternate desktop or home screen** *(guideline text; no rejection in this record)* —
apps that create alternate desktop or home screen environments are rejected.

Launcher shells, fake SpringBoard replacements, and "replace your home screen" products fail.
Fix: ship as a normal app UI, not an OS skin.

**2.5.9 Standard switches and expected UI behavior** *(guideline text; no rejection in this
record)* — apps must not alter or disable standard switches (Volume, Ring/Silent) or other
native UI elements or behaviors. Blocking links out to other apps or breaking expected system
behaviors is called out.

Do not trap users inside a WebView with no escape, swallow volume hardware events for gimmicks,
or break universal links without cause. Fix: restore system behavior and expected outbound
links.

**2.5.10 Intentionally omitted** *(guideline text; no rejection in this record)* — Apple marks
2.5.10 as intentionally omitted. No active requirement. Verify on the live page if something
cites it.

**2.5.11 SiriKit and Shortcuts** *(guideline text; no rejection in this record)* — integrate
only intents the app can fulfill alone and that users would expect from the stated product (a
meal planner must not expose a workout intent just because a partner SDK exists). Plist
vocabulary must pertain to your app; aliases must relate to your app or company name — no
generic terms or third-party app names. Resolve requests directly: no ads between request and
fulfillment; disambiguate only when required to finish the task.

Audit intent donations, shortcut phrases, and any interstitial that monetizes a Siri handoff.
Fix: prune unrelated intents, clean aliases, remove ad gates on fulfillment.

**2.5.12 CallKit and SMS fraud extensions** *(guideline text; no rejection in this record)* —
CallKit or SMS Fraud Extension blocking must only block numbers confirmed as spam. Call, SMS,
and MMS blocking or spam identification features must be described in marketing text with the
criteria for lists. Data from these tools may only operate or improve the app or extension —
no selling, tracking, or profile-building side use.

Clearing review: document list criteria, prove spam confirmation process, strip secondary data
monetization, and match App Privacy answers.

**2.5.13 Facial recognition for authentication** *(guideline text; no rejection in this
record)* — account authentication with face recognition must use LocalAuthentication where
possible (not ARKit or other face tech), and must offer an alternate authentication method for
users under 13.

Do not roll your own face login on the camera for auth. Use system biometrics and keep a
password or other alternate path for kids.

**2.5.14 Recording indicators and consent** *(guideline text; no rejection in this record)* —
explicit user consent plus a clear visual and/or audible indication are required when recording,
logging, or otherwise making a record of user activity — camera, microphone, screen recording,
or other inputs.

Silent recording, hidden screen capture, or logging sessions without chrome fails. Fix: system
indicators where provided, in-app recording UI that cannot be missed, and consent before the
first record.

**2.5.15 Files and iCloud documents in pickers** *(guideline text; no rejection in this
record)* — apps that let users view and select files should include items from the Files app
and the user's iCloud documents.

A picker that only shows the app sandbox while advertising "open files" fails. Use document
picker / Files integration and enable iCloud documents when you claim cloud files.

**2.5.16 Widgets, extensions, notifications, and App Clips** *(guideline text; no rejection in
this record)* — widgets, extensions, and notifications must relate to the app's content and
functionality. All App Clip features and functionality must exist in the main app binary. App
Clips cannot contain advertising.

Orphan widgets that upsell unrelated products, notification spam disconnected from app value,
and Clip-only features that never appear in the full app fail. Fix: align extension surfaces to
core features, mirror Clip capabilities in the main binary, remove Clip ads.

**2.5.17 Matter pairing** *(guideline text; no rejection in this record)* — apps that support
Matter must use Apple's support framework for Matter to initiate pairing. Any Matter software
component other than Apple's Matter SDK must be Connectivity Standards Alliance certified for
the platform it runs on.

Do not ship a custom pairing stack that bypasses Apple's Matter support. Document CSA
certification in notes when you use non-Apple Matter components.

**2.5.18 Display advertising rules** *(guideline text; no rejection in this record)* — display
ads belong in the main app binary only — not in extensions, App Clips, widgets, notifications,
keyboards, or watchOS apps. Ads must fit the app's age rating; users must be able to see all
information used to target them without leaving the app; no targeted or behavioral ads from
sensitive data (HealthKit, ClassKit, Kids Category, and similar). Interstitials must be clearly
ads, must not trick taps, and must offer large, visible close/skip controls. Apps with ads must
let users report inappropriate or age-inappropriate ads.

Audit mediation SDKs for extension surfaces, health-segment targeting, and dark-pattern
closers. Fix: confine ads to main binary, add report-ad UI, enlarge skip, and strip sensitive
targeting.

### Safety

**1.1 Objectionable Content** *(guideline text; no rejection in this record)* — the umbrella
for content that is offensive, insensitive, upsetting, intended to disgust, in exceptionally
poor taste, or just plain creepy. Reviewers apply it to the binary, the listing, ads inside the
app, generative output, and anything users can post. A 1.1 citation with a vague body usually
means one of the 1.1.x subtypes below; read the body before you rewrite the whole product.

Surfaces that fail first: App Store screenshots and the icon, the first launch screen, share
cards, sticker or meme packs, AI prompts with weak filters, and UGC that 1.2 moderation never
actually catches. Edgy branding that works on the open web often dies here.

Response pattern that works:

1. Identify the exact asset or flow named (or implied) in the rejection.
2. Remove or redesign it — arguing taste against 1.1 almost never wins.
3. If the risk is generative or UGC, prove the 1.2 filter / report / block / contact stack with
   tap paths in the notes and a screen recording of a report.
4. Resubmit with notes that name what was removed and what remains.

Do not appeal a pure taste call. Appeal only if the reviewer cites content the app does not
contain — then it is a fact fight under [APPEALS.md](APPEALS.md).

**1.1.1 Defamatory or discriminatory content** *(guideline text; no rejection in this record)* —
defamatory, discriminatory, or mean-spirited content aimed at religion, race, sexual
orientation, gender, national or ethnic origin, or another targeted group, especially when the
app is likely to humiliate, intimidate, or harm that group. Professional political satirists
and humorists are generally exempt. A consumer utility, game, or social app that "roasts"
protected groups does not get that exemption.

What reviewers hunt: onboarding jokes, character names, enemy factions modeled on real peoples,
ranking or hot-or-not flows aimed at a demographic, sticker packs, and AI personas instructed to
insult a group.

Clear it by deleting the targeting, not by adding a disclaimer. If the product is genuine
satire under the exemption, say so once in the notes with who you are and why the exemption
applies — then stop arguing. Pair with 1.2 if users can generate the same material themselves.

**1.1.2 Graphic violence and targeted "enemies"** *(guideline text; no rejection in this
record)* — realistic portrayals of people or animals being killed, maimed, tortured, or abused,
or content that encourages violence. Inside games, enemies cannot solely target a specific real
race, culture, government, corporation, or other real entity.

The reviewer often never reaches deep levels. Treat trailers, screenshots, App Preview, and the
first five minutes as the entire case. Photoreal torture, animal cruelty, and "kill the [real
group]" framing fail even when the age rating is high.

Fixes that land: reduce realism, fictionalize factions, broaden targets beyond one real entity,
and replace screenshot sets that lead with gore. Age rating up does not cure a 1.1.2 problem.
Encouraging real-world violence is a removal-class issue; treat it like one.

**1.1.3 Weapons and dangerous objects** *(guideline text; no rejection in this record)* —
depictions that encourage illegal or reckless use of weapons and dangerous objects, or that
facilitate purchase of firearms or ammunition. A history museum app or a clearly fictional
shooter is a different product from a how-to, a reckless-use celebration, or a storefront.

Audit every path that ends in real-world acquisition: affiliate links, "buy ammo" buttons,
QR codes to dealers, deep links to gun marketplaces, and IAP that unlocks real purchase flows.
Also audit copy that coaches unsafe handling.

Fix by cutting purchase facilitation and reckless coaching. If the app is education or fiction,
say that in the notes with the exact screens that prove the boundary, and remove any ambiguous
commerce. Borderline "tactical" consumer apps get rejected when the commerce link is one tap
away — distance is not a defense if the link is in the app.

**1.1.4 Sexual and pornographic material** *(guideline text; no rejection in this record)* —
overtly sexual or pornographic material: explicit descriptions or displays of sexual organs or
activities intended to stimulate erotic rather than aesthetic or emotional feelings. Explicitly
includes hookup apps and apps that may include pornography or facilitate prostitution, human
trafficking, or exploitation.

Dating, matchmaking, and LGBTQ+ social products can ship. Escort directories, explicit media
libraries, "arrangement" marketplaces, and NSFW feeds that are on by default do not. Screenshots
alone can sink the submission — never put explicit frames in the listing.

Clearing a 1.1.4:

- Strip explicit media from the binary and the listing.
- Turn mature UGC off by default; require an intentional opt-in; moderate under 1.2.
- Set an honest age rating; lying down gets you a worse problem later.
- State in reviewer notes what the app does not do: no prostitution, no porn catalog, no
  trafficking facilitation.
- Trafficking or exploitation facilitation is not a resubmit-with-polish event — remove the
  capability and expect account-level heat if it was real.

**1.1.5 Inflammatory religious commentary** *(guideline text; no rejection in this record)* —
inflammatory religious commentary, or inaccurate or misleading quotations of religious texts.
Scripture study, comparative religion, and faith community apps are routine when citations are
accurate and the tone is not bait.

Failure modes: fabricated quotes, quotes cut to invert meaning, share cards designed to
provoke, and AI "daily verse" features with no source chain. Hate dressed as theology also
collides with 1.1.1.

Fix: cite real editions or translations, show sources in-product, kill gotcha framing, and
keep generative religion features on a tethered corpus. Notes should point at one example screen
with a visible citation.

**1.1.6 False information and trick features** *(guideline text; no rejection in this record)* —
false information and features, including inaccurate device data or trick/joke functionality
such as fake location trackers. Stating the app is "for entertainment purposes" does not
overcome this guideline. Apps that enable anonymous or prank phone calls or SMS/MMS messaging
are rejected.

This is one of the cleanest automatic rejects in Safety. Fake radar, fake virus scanners, fake
police trackers, and prank callers fail even with comedy framing. The test is deception: if a
reasonable user could believe the device is measuring something it is not, or that a message
came from someone it did not, you lose.

There is no notes-only rescue for prank telephony. Remove it. For novelty apps that remain,
make the fiction unmistakable on every screen (not only in the subtitle), never spoof system UI,
and never touch real SMS/call identity. Replace "entertainment purposes" disclaimers with
product changes.

**1.1.7 Capitalizing on tragedies** *(guideline text; no rejection in this record)* — harmful
concepts that capitalize or seek to profit on recent or current events such as violent
conflicts, terrorist attacks, and epidemics. Straight news reporting and relief information can
belong; opportunistic monetization timed to the body count does not.

Audit IAP names, limited-time skins, iconography, push copy, and launch dates against current
events. A "charity" upsell that never leaves the developer's pocket is worse than no upsell.

Fix: delay or cancel the opportunistic SKU, ship factual reporting without gore-as-marketing,
and if you collect donations, document the real recipient. Do not argue that satire or awareness
excuses a paid skin tied to a massacre.

**1.2 User-Generated Content** *(guideline text; no rejection in this record)* — any app with
user content or social features must include all four of these, and reviewers check for each:

1. A method for filtering objectionable material from being posted
2. A mechanism to report offensive content, plus timely responses to those reports
3. The ability to block abusive users
4. Published contact information so users can reach you

Missing any one is a rejection. Beyond that, an app that ends up used primarily for pornography,
random or anonymous chat, "hot-or-not" style objectification of real people, physical threats, or
bullying "may be removed without notice" — no rejection cycle, no appeal window. Egregious or
repeated failure to moderate is grounds for removal from the Developer Program.

Put the tap path to filter settings, report, block, and contact in the reviewer notes on the
first submission. A buried webform that takes five days to answer fails the "timely responses"
bar even if the button exists.

**1.2.1 Creator content** *(guideline text; no rejection in this record)* — if the app hosts
content from a community of creators, that content is judged as user-generated content, not as
native apps coded by developers. Creator experiences must not change the core features and
functionality of the native app — they add content inside structured experiences (video,
articles, audio, even casual games). Payments still follow 3.1.1. Communicate which creator
content needs additional purchases.

The app must let users identify content that exceeds the app's age rating and must use an age
restriction mechanism based on verified or declared age to limit underage access. Moderate
under 1.2. If creators can ship mini-apps that alter native behavior, you have a 1.2.1 problem
and likely a 4.7 problem at the same time.

**1.3 Kids Category, with 5.1.4** *(guideline text; no rejection in this record)* — the
strictest tier on the store, and the requirements survive leaving the category:

- No links out, no purchasing opportunities, no other distractions unless behind a parental gate
- No third-party analytics and no third-party advertising, with narrow exceptions: analytics that
  transmit no IDFA and nothing identifying a child or their device, and contextual advertising
  from providers with published Kids Category policies including human review of creatives
- No personally identifiable information or device information to third parties at all
- A privacy policy, plus compliance with COPPA, GDPR, and local equivalents

Two traps. Once customers expect Kids Category behavior, later updates must keep meeting it even
if you deselect the category. And a parental gate is not the same thing as verified parental
consent for data collection — 5.1.4 requires the latter separately.

**1.4 Physical Harm** *(guideline text; no rejection in this record)* — reject apps that behave
in a way that risks physical harm. This is the parent; the actionable work almost always lives
in 1.4.1–1.4.5. A bare "1.4" in Resolution Center still needs you to map the body to a subtype
before you change code.

Common parent-level patterns: medical claims without trust, substance encouragement, reckless
driving features, and IRL dares. Do not answer with a generic safety essay. Name the screen,
remove the risk, and resubmit. Legal and safety violations also block the bug-fix fast path
described under "When a rejection arrives," so treat any 1.4 as blocking.

**1.4.1 Physical Harm, health and medical** — two canned rejections that fire on health and
wellness apps by default:

> Your app provides medical diagnoses or treatment advice but does not include the required
> medical disclaimer.

> Your app provides health or medical recommendations, calculations, references, wellness
> reports, or diagnoses without including the sources of the recommendations.

Both arrive even when the disclaimer and citations are already shipped. Over-comply visibly:
the disclaimer as its own first-launch screen rather than only in the description and
settings, source attribution and a tappable link inside every single answer without
scrolling, and reviewer notes stating where the disclaimer appears with one example answer and
its source.

From the current guidelines, also enforce: apps that claim to take x-rays or measure blood
pressure, body temperature, blood glucose, or blood oxygen using only the sensors on the device
are not permitted; remind users to check with a doctor before medical decisions; if you have
regulatory clearance, submit a link to that documentation with the app. Sensor-only diagnostic
theater cannot be notes-lawyered away — remove the claim.

**1.4.2 Drug dosage calculators** *(guideline text; no rejection in this record)* — drug dosage
calculators must come from the drug manufacturer, a hospital, university, health insurance
company, pharmacy, or other approved entity, or receive approval from the FDA or one of its
international counterparts. Apple's stated concern is long-term support and patient harm, not
whether your unit tests pass.

Who fails: indie ports of formulas from blogs, unverified "clinical" calculators, and apps that
cannot show institutional backing. Who can pass: calculators shipped by or for an approved
entity, with clearance attached when required, and a realistic update story.

Submission pack that clears review:

- Name the approving entity in the notes and on a credentials screen.
- Attach FDA or counterpart clearance links when you have them.
- Explain who maintains the formulas and how often they are reviewed.
- If you are not an approved entity, remove the calculator. Linking out to an authoritative
  external tool is safer than shipping an unofficial one.

**1.4.3 Tobacco, alcohol, and controlled substances** *(guideline text; no rejection in this
record)* — apps may not encourage consumption of tobacco and vape products, illegal drugs, or
excessive amounts of alcohol. Apps that encourage minors to consume any of these are rejected.
Facilitating sale of controlled substances is not allowed except for licensed pharmacies and
licensed or otherwise legal cannabis dispensaries. Facilitating sale of tobacco is not allowed.

Encouragement is broader than a buy button. Drinking streaks, "shot" games aimed at teens,
glamorized illegal drug content, and rewards for consumption all count. Informational content
about addiction recovery or legal regulations is a different shape — keep the tone non-celebratory
and keep minors out of the funnel.

For licensed pharmacy or legal dispensary commerce: put license proof in Review Information,
restrict storefronts to legal regions, and age-gate hard. For everyone else: no checkout for
restricted goods, no tobacco sales facilitation, strip encouragement copy, and say in notes
what the app does not sell or promote.

**1.4.4 DUI checkpoints and reckless driving** *(guideline text; no rejection in this record)* —
apps may only display DUI checkpoints that are published by law enforcement agencies, and must
never encourage drunk driving or other reckless behavior such as excessive speed.

Crowd-sourced "cop ahead" maps, unmarked user pin drops, and gamified speeding are the classic
rejects. If checkpoint data cannot be traced to a named LE publication, remove it.

Clear path:

- Source checkpoints only from law enforcement publications; cite the source in-product.
- Delete UX that rewards speeding, evade-police framing, or driving while interacting.
- If the app is navigation, keep attention patterns consistent with safe driving (audio-first,
  large controls) and say so in the notes.

**1.4.5 Activities that risk physical harm** *(guideline text; no rejection in this record)* —
apps should not urge customers to participate in activities (like bets, challenges, and similar)
or to use their devices in a way that risks physical harm to themselves or others.

Think distracted walking or driving challenges, AR overlays that demand eyes-down in traffic,
IRL dare campaigns, "hold the phone while you…" stunts, and social bets that push unsafe
behavior. The rejection is about urging risk, not about every fitness app.

Fixes: remove the dare; if a motion-heavy feature must exist, hard-stop when driving is detected
or when the user is moving unsafely; never incentivize ignoring surroundings. Document the hard
stop in notes with a screen recording. Pair with 1.1.2 / 1.4.3 when the dare is violent or
substance-related.

**1.5 Developer Information** *(guideline text; no rejection in this record)* — people need to
know how to reach you with questions and support issues. The app and its Support URL must
include an easy way to contact you. This is particularly important for apps that may be used in
the classroom. Inaccurate or stale contact information frustrates customers and may violate the
law in some countries or regions. Wallet passes must include valid contact information from the
issuer and must be signed with a dedicated certificate assigned to the brand or trademark owner
of the pass.

Pre-submit check (do not skip):

1. Open the App Store Connect Support URL in a private browser — no redirect to a dead page.
2. From cold launch, reach Help or Contact in a few taps; send a test and confirm delivery.
3. Confirm App Store Connect account contact fields match reality.
4. For Wallet: issuer contact on the pass, correct signing cert for the brand.

A beautiful in-app chat that is empty, or a `mailto:` that bounces, fails this guideline. Fix
the path before arguing. Classroom and education apps get less patience here — put contact above
the fold.

**1.6 Data Security** *(guideline text; no rejection in this record)* — apps should implement
appropriate security measures to ensure proper handling of user information collected under the
Apple Developer Program License Agreement and these Guidelines (see Guideline 5.1) and to
prevent unauthorized use, disclosure, or access by third parties.

1.6 is Safety's hook into privacy engineering. It rarely arrives as a philosophy debate; it
arrives because something looked leaky: cleartext traffic, tokens in logs, world-readable
databases, an SDK phoning home credentials, or a breach pattern the reviewer can smell from
the binary and privacy answers.

Answer with controls, not slogans:

- TLS for data in transit; no sensitive payloads on plain HTTP.
- Secrets and session tokens out of logs, analytics, and crash reports.
- Least privilege for third-party SDKs; remove SDKs that do not need PII.
- Access controls on stored PII; delete flows that actually delete (see 5.1.1).
- App Privacy answers that match the binary (see 5.1.2).

If cited, patch the exposure, rotate anything that may have leaked, and put the concrete fix in
the notes. Pointing at a privacy policy PDF without a binary change does not clear 1.6.

**1.7 Reporting Criminal Activity** *(guideline text; no rejection in this record)* — apps for
reporting alleged criminal activity must involve local law enforcement, and can only be offered
in countries or regions where such involvement is active.

This is narrow and bright-line. A tip line, witness app, or "report a crime" button with no live
law-enforcement program behind it fails. A news app that links to an official police portal is
not the same product — do not brand it as in-app crime reporting unless the LE relationship is
real.

To ship:

- Limit storefronts to regions with active LE involvement.
- Attach proof (program letter, agency page, contract summary) in Review Information.
- Make the in-app flow honest about who receives the report and when.
- If you cannot show active involvement, remove reporting or reposition as non-reporting
  information only.

Do not expand into regions first and "add partnerships later." Geo first, feature second.

### Business model

Keep IAP out of the first submission entirely — see [Sequencing](#sequencing-what-to-ship-in-the-first-submission).

If the business model is not obvious, explain it in metadata and App Review notes. Opaque
monetization delays review. Apple also rejects clear rip-offs and irrationally high prices, and
will expel developers who manipulate reviews or chart rankings with paid, incentivized,
filtered, or fake feedback (including via third parties) — that overlaps 5.6.3.

**3.1 Payments** *(guideline text; no rejection in this record)* — parent for how money moves
in the app. Most citations name 3.1.1–3.1.5; map the body to the child before you rebuild
checkout. The recurring decision is simple: digital goods and features unlocked in the app use
IAP unless a written 3.1.3 exception applies; physical / outside-the-app consumption does not.

**3.1.1 In-App Purchase** *(guideline text; no rejection in this record)* — unlocking features
or content inside the app requires in-app purchase. Apps "may not use their own mechanisms to
unlock content or functionality, such as license keys, augmented reality markers, QR codes,
cryptocurrencies and cryptocurrency wallets."

This is the highest-frequency rejection in the store and the easiest to walk into by accident.
Specific traps in the current text:

- Credits and in-game currencies bought via IAP may never expire, and restorable purchases need
  a restore mechanism.
- Digital gift cards, vouchers, and coupons redeemable for digital goods must be sold via IAP.
  Physical gift cards mailed to customers may not.
- Loot boxes and any randomized purchase must disclose the odds of each item before purchase.
- A non-subscription free trial is done with a Price Tier 0 non-consumable named "XX-day Trial,"
  and the app must state the duration, what stops working at the end, and any later charges up
  front.
- NFTs may be sold via IAP, but NFT ownership may not unlock app features, and outside the US
  storefront there may be no links or calls to action pointing at other purchase mechanisms.
- Tip jars for the developer or digital creators use IAP currencies.
- Gifting of IAP-eligible items is allowed; refunds go only to the original purchaser; no
  exchanges.
- Mac App Store apps may enable plug-ins or extensions with non–App Store mechanisms.

**3.1.1(a) External purchase links** *(guideline text; no rejection in this record)* — linking
out to your own site to sell digital content normally requires a StoreKit External Purchase Link
Entitlement, and is limited to specific storefronts. **The United States storefront is exempt:**
buttons, external links, and other calls to action to outside purchase mechanisms are permitted
there without the entitlement. Everywhere else, absent the entitlement, they are grounds for
rejection. Reader apps and music streaming apps have their own separate entitlements.

Music Streaming Services Entitlements (specific regions) can expose a link or buy button to the
developer's site for digital music, and may collect email solely to send that purchase link.
Entitlement misuse, scams, or fraud tied to these links can remove the app and the developer
account.

This area has changed repeatedly under legal pressure and is the single most likely part of this
file to be out of date. Verify against the live page before acting on it.

**3.1.2 Auto-renewing subscriptions** — the most expensive repeat offender, and the one most
likely to be rejected twice for different stated reasons.

A subscription must deliver recurring, ongoing value, not act as a paywall on static content.
The argument that works: name the ongoing cost the subscription funds — servers, alerting,
push, a content pipeline — and the ongoing value the user receives for it. A design that has
passed: an ongoing-value content surface, a real free tier metered per month, unlimited access
on subscription. Apple's own examples include new game levels, episodic content, multiplayer,
substantive ongoing updates, large or continually updated media libraries, SaaS, and cloud
support. Subscriptions may sit beside à la carte purchases.

Rejections here can also cite auto-renew in an app that has none. Do not assume the citation
describes your build.

Hard constraints in the current text that are easy to violate by design rather than by accident:

- The subscription period must be **at least seven days**.
- It must **work across all of the user's devices** where the app is available.
- The user must get what they paid for **without performing additional tasks** — no posting to
  social media, no uploading contacts, no minimum number of check-ins.
- Moving an existing paid app to subscriptions must not take away functionality existing users
  already bought. Grandfather the prior unlock.
- Upgrade and downgrade paths must not let a user accidentally subscribe to two variants of the
  same thing (3.1.2(b)).
- Before the subscribe sheet, describe what the price buys — issues per month, storage, access
  level — matching Schedule 2 of the Developer Program License Agreement (3.1.2(c)).
- Streaming-game service subscriptions shared across third-party apps must still download from
  the App Store, avoid duplicate payment, and not disadvantage non-subscribers.
- Carrier-bundled music/video (and some other) subscriptions need prior Apple approval and must
  terminate with the data plan; no consumable discounts inside those bundles.

Apple also names bait-and-switch and "trick users into purchasing a subscription under false
pretenses" as grounds for removal from the store and from the Developer Program, which is the
account-level version of the paywall-clarity problem described below.

**3.1.3 Other purchase methods** *(guideline text; no rejection in this record)* — the written
exceptions where payment methods other than IAP are allowed. Apps in this section still may not
encourage non-IAP purchasing **inside** the app, except on the United States storefront and as
allowed under 3.1.1(a) and 3.1.3(a). You may email or otherwise message your user base outside
the app about other purchase methods.

If you are not clearly inside one lettered exception, assume 3.1.1 wins. Document which
exception you claim in the reviewer notes with evidence.

**3.1.3(a) Reader apps** *(guideline text; no rejection in this record)* — magazines, newspapers,
books, audio, music, and video previously purchased or subscribed elsewhere may be accessed in
the app. Reader apps may offer free-tier account creation and account management for existing
customers. The External Link Account Entitlement can add an informational link to the
developer's site for create/manage account flows; the US storefront does not require that
entitlement for buttons and CTAs.

Not a loophole for general SaaS. If the product is not primarily previously purchased media
access, you are not a reader app. In-app encouragement to buy on the web still fails outside
the US / entitlement rules.

**3.1.3(b) Multiplatform services** *(guideline text; no rejection in this record)* — cross-
platform products may let users access content, subscriptions, or features bought on other
platforms or the web — including consumables in multi-platform games — **provided those items
are also available as IAP inside the app**.

Mirror the catalog. Web-only SKUs that unlock the iOS app without an IAP twin fail. Say in
notes how restore / account login brings cross-platform entitlements and where the matching IAP
lives.

**3.1.3(c) Enterprise services** *(guideline text; no rejection in this record)* — apps sold
only by you directly to organizations or groups for employees or students (professional
databases, classroom tools) may let enterprise users access previously purchased content or
subscriptions. Consumer, single-user, and family sales must use IAP.

B2B-only positioning must match distribution reality. A "enterprise" SKU that anyone can buy
on the store is still consumer IAP territory.

**3.1.3(d) Person-to-person services** *(guideline text; no rejection in this record)* —
real-time person-to-person services between two individuals (tutoring, medical consults, real
estate tours, fitness training) may use non-IAP payment. One-to-few and one-to-many real-time
services must use IAP.

The headcount of the live session matters. Group classes and multi-client live streams are IAP.
Document the 1:1 real-time nature in notes.

**3.1.3(e) Goods and services outside the app** — physical goods or free shipping bundled into
a digital subscription gets rejected. Keep physical fulfillment out of the IAP entitlement.

Note the direction of this rule, which trips people up: anything consumed outside the app
*must not* use in-app purchase, and must be collected another way such as Apple Pay or card
entry. It is the mirror image of 3.1.1.

Historical note for anyone reading an old rejection: this was cited as 3.1.5 for years. Apple
renumbered — verify the number on the live page before quoting it back.

**3.1.3(f) Free stand-alone companions** *(guideline text; no rejection in this record)* — free
apps that are stand-alone companions to a paid web tool (VoIP, cloud storage, email, web
hosting) need not use IAP **if there is no purchasing in the app and no CTA to purchase
outside the app**.

A companion that upsells plans with a "Manage on web" buy button fails outside US / entitlement
exceptions. Pure sync clients with zero commerce chrome pass.

**3.1.3(g) Advertising management apps** *(guideline text; no rejection in this record)* — apps
whose sole purpose is letting advertisers buy and manage campaigns across media (TV, outdoor,
web, apps) may skip IAP. They manage campaigns; they do not display those ads. Digital
purchases for content experienced in-app — including buying ads to show in the **same** app
(social "boosts") — must use IAP.

Campaign-manager vs in-app promotion is the line. Boosts, bump, and promote-my-post are IAP.

**3.1.4 Hardware-specific content** *(guideline text; no rejection in this record)* — when
features depend on specific hardware to function, the app may unlock that functionality without
IAP (example: astronomy features when synced with a telescope). Features that optionally work
with an approved physical product (such as a toy) may unlock without IAP **if an IAP option is
also available**. You may not require unrelated product purchases or force advertising /
marketing tasks to unlock app functionality.

Valid: telescope sync unlocks sky features; optional toy unlocks extras while IAP remains for
users without the toy. Invalid: "buy any SKU from our store to unlock," "share to unlock," or
hardware theater that is really a digital paywall.

**3.1.5 Cryptocurrencies** *(guideline text; no rejection in this record)* — crypto-specific
payment and storage rules:

- **Wallets** — allowed only from developers enrolled as an **organization**.
- **Mining** — on-device mining banned; off-device / cloud mining only.
- **Exchanges** — only where you hold appropriate licensing; limit storefronts to those regions.
- **ICOs / crypto futures / crypto-securities** — only from established banks, securities firms,
  FCMs, or other approved financial institutions, and must follow applicable law.
- **Task payouts** — may not pay crypto for downloading apps, recruiting users, posting to
  social networks, or similar tasks.

Individual-enrolled wallet apps fail. Attach licenses in Review Information for exchange
features. Pair with 3.2.1(viii) / 3.2.2(viii) when the product is trading rather than a simple
wallet.

**3.2 Other business model issues** *(guideline text; no rejection in this record)* — additional
acceptable and unacceptable business patterns beyond payments. The lists are not exhaustive;
your submission can still change policy. Prefer the 3.2.1 / 3.2.2 child in responses.

**3.2.1 Acceptable business patterns** *(guideline text; no rejection in this record)* —

- Show your own apps for purchase or promotion inside the app, but not as a mere catalog of
  everything you publish.
- Recommend a curated set of third-party apps for a specific approved need (health, aviation,
  accessibility) with real editorial content — not a bare storefront.
- Expire access to approved rental content (films, TV, music, books) after the rental window;
  other items and services may not expire.
- Wallet passes for payments, offers, or identification (tickets, coupons, VIP). Other Wallet
  uses risk app rejection and Wallet credential revocation.
- Insurance apps: free, legally compliant in distributed regions, no IAP.
- Approved nonprofits may fundraise in their own or third-party apps with Apple Pay, disclosed
  fund use, legal compliance, and donor tax receipts. Nonprofit platforms must list only
  nonprofits that completed Apple's nonprofit approval.
- Optional person-to-person monetary gifts with 100% to the receiver may skip IAP; any gift
  tied to digital content or services must use IAP.
- Financial trading, investing, or money-management apps must be submitted by the institution
  performing the service, with licenses in every location you ship.

**3.2.2 Unacceptable business patterns** *(guideline text; no rejection in this record)* —

- Interfaces that display third-party apps, extensions, or plug-ins like an App Store or as a
  general-interest collection.
- (ii) and (vi) intentionally omitted by Apple — ignore stale citations; fetch the live page.
- Artificially inflating ad impressions or click-throughs, or apps designed predominantly to
  display ads.
- Collecting charity funds in-app unless you are an approved nonprofit under 3.2.1(vi). Other
  fundraising apps must be free and collect only outside the app (Safari, SMS).
- Arbitrarily restricting who may use the app by location or carrier.
- Artificially manipulating a user's visibility, status, or rank on other services unless that
  service's terms allow it.
- Binary options trading — not permitted; use the web. CFDs / derivatives / FOREX require proper
  licensing in every jurisdiction served.
- Personal loan apps must conspicuously disclose all terms including max APR and due date; max
  APR including fees may not exceed **36%**; may not require full repayment in **60 days** or
  less.
- Do not force rating, reviewing, downloading other apps, or other store actions to unlock the
  app. In-app incentives for gameplay or watching an ad remain allowed.

**Paywall disclosure** — the Paid Apps Agreement requires nine specific disclosures in literal
on-screen text on the purchase screen. Full checklist with approved wording in
[TEMPLATES.md](TEMPLATES.md). Call to action should read "Subscribe," any free trial should be
stated plainly, and none of the disclosure text may be clipped or require scrolling inside a
constrained scroll view.

### Design and originality

Apple wants simple, refined, innovative, easy-to-use apps. Design quality is on you; the rules
below are the floor for approval. Apps that stop working or degrade after approval can be
removed at any time — keep shipping.

**4.0 Design** — one number, two completely different rejections. Always read the body. Apple's
page leads with Design as a section rather than a "4.0" anchor; Resolution Center still uses
4.0 for subjective quality.

Image and layout quality: "Your app included low resolution or jagged images. Your app
included hard to read type or typography. Screens were crowded and/or laid out in a way that
made it difficult to complete tasks." Usually arrives with no examples attached. Ship @3x
assets, a real type scale, and uncrowded screens.

Sign in with Apple button styling: "White buttons should not be on light backgrounds. Black
buttons and white buttons with a black outline should not be on dark backgrounds." Use
`ASAuthorizationAppleIDButton` with the style matched to the surface. Never hand-build the
button.

Do not appeal pure taste. Fix the UI or escalate only when the finding is factually wrong about
what is on screen. Phone-call questions for vague 4.0 are in [APPEALS.md](APPEALS.md).

**4.1 Copycats** — come up with your own ideas. Do not clone the latest popular app or make
cosmetic renames of someone else's UI. Beyond IP risk, it clutters the store.

- **(a)** Original product required — minor name/UI tweaks on another's app fail.
- **(b)** Impersonating another app or service is a Developer Code of Conduct violation and can
  remove you from the Program.
- **(c)** Do not use another developer's icon, brand, or product name in your icon or name
  without that developer's approval.

Third-party logos, brand names, and recognizable art in screenshots, icons, or the binary also
fail here and under 5.2. Fix by redesigning assets and names, not by adding a disclaimer.

**4.2 Minimum Functionality** — your app needs features, content, and UI beyond a repackaged
website. If it is not useful, unique, or "app-like," it does not belong. Thin entertainment or
utility may be refused. A song or movie alone belongs on the iTunes Store; a book or game guide
alone belongs on Apple Books.

The answer that works for borderline apps: a written feature count against the nearest
competitor in the reviewer notes, plus one nameable thing competitors lack. Pair with
Sequencing — shipping too narrow can look like 4.2 even when you meant focus.

**4.2.1 ARKit depth** *(guideline text; no rejection in this record)* — ARKit apps must deliver
rich, integrated AR experiences. Dropping a model into an AR view or replaying an animation is
not enough.

Audit the first session: persistent interaction, environment understanding, and a reason AR is
required. Fix by deepening the AR loop or dropping ARKit if it is a gimmick overlay.

**4.2.2 Not primarily marketing** — other than catalogs, apps should not primarily be marketing
materials, advertisements, web clippings, content aggregators, or a collection of links.

A wrapper around a feed, a single-purpose utility brochure, or a WebView of the marketing site
reads as insufficient. Ship native behavior and lasting utility; put the competitor comparison
in the notes.

**4.2.3 Standalone launch and download disclosure** *(guideline text; no rejection in this
record)* —

- **(i)** The app must work on its own without requiring installation of another app.
- **(ii)** If additional resources must download before the app works on first launch, disclose
  the download size and prompt the user before starting.

Companion-required shells fail (i). Silent multi-hundred-MB asset pulls fail (ii). Fix: merge
required capability into one binary, or show size + consent before the download.

**4.2.4 Intentionally omitted** *(guideline text; no rejection in this record)* — Apple marks
4.2.4 as intentionally omitted. No active requirement. Treat old citations as stale.

**4.2.5 Intentionally omitted** *(guideline text; no rejection in this record)* — Apple marks
4.2.5 as intentionally omitted. No active requirement. Fetch the live page if something cites it.

**4.2.6 Template and app-generator apps** *(guideline text; no rejection in this record)* —
apps from a commercialized template or app-generation service are rejected unless submitted
**directly by the provider of the app's content**. Template vendors must not submit on behalf
of clients; they should give clients tools for customized, unique apps. Acceptable alternative:
one binary hosting all client content in an aggregated or picker model (restaurant finder with
per-client pages; event app with per-client events).

White-label mills and "we'll publish for you" shops fail. Content owners submit their own
apps, or the vendor ships one picker binary. This pairs with 4.3 spam when many near-duplicates
appear from one account.

**4.2.7 Remote desktop clients** *(guideline text; no rejection in this record)* — if the client
mirrors specific software or services rather than a generic host desktop, it must:

- Connect only to a user-owned PC or dedicated game console on a local / LAN network.
- Execute software only on the host; render the host screen; use only APIs needed to stream.
- Start account creation and management on the host, not the client.
- Avoid iOS / App Store–like UI, store browsing, or purchasing software the user does not
  already own/license. Transactions inside mirrored software need not use IAP if processed on
  the host.
- Thin clients for cloud-based apps are not appropriate for the App Store.

Generic remote desktop to the user's machine is a different shape than cloud game/streaming
storefronts — those fail (e) and often 4.7.

**4.3 Spam** — "We noticed that your app provides the same feature set as other apps submitted
to the App Store; it simply varies in content or language, which is considered a form of spam."

Fatal for template-driven portfolios: many apps off one codebase with swapped content is the
trigger. Reviewers decline to identify which apps or which features, and the App Review Board
tends to uphold it. Differentiation must be real, nameable, and shipped. Pulling near-duplicate
apps from the store is part of the answer.

Note that this finding can be called a mistake by a reviewer on a phone call and then be
re-issued days later.

**4.3(a)** — do not ship multiple bundle IDs of the same app varying by city, team, or
university. Use one app with variations as IAP.

**4.3(b)** — do not submit apps indistinguishable from what is already widely available. Apple
will not accept new **dating, flashlight, sound effects, wallpaper, simple timers, or fortune
telling** apps without a meaningfully different or improved experience, and may remove stale
ones. **Drinking games, Kama Sutra, fart, and burp** apps are called out as low-effort; repeated
submissions can mean Program removal.

Building in those six established categories makes the differentiation argument mandatory from
version 1.0.

**4.4 Extensions** *(guideline text; no rejection in this record)* — apps that host extensions
must follow the App Extension Programming Guide (and Safari app / web extension docs as
applicable), include helpful functionality such as help and settings where possible, accurately
disclose available extensions in marketing text, and keep marketing, advertising, and IAP out of
the extension itself.

**4.4.1 Keyboard extensions** *(guideline text; no rejection in this record)* — keyboards must
provide typed input; follow Sticker rules if they include images/emoji; offer a way to move to
the next keyboard; work without full network access and without requiring full access; and
collect user activity only to improve the keyboard on-device. They must not launch apps other
than Settings, or repurpose keys for unrelated behaviors (for example holding Return to open
the camera).

**4.4.2 Safari extensions** *(guideline text; no rejection in this record)* — must run on the
current Safari for that OS, must not interfere with System or Safari UI, and must never include
malicious or misleading content or code (Program removal territory). Claim access only to the
sites strictly needed.

**4.4.3 Intentionally omitted** *(guideline text; no rejection in this record)* — no active
requirement under this number.

**4.5 Apple sites and services** *(guideline text; no rejection in this record)* — parent for
how apps may use Apple RSS, Apple Music, Game Center, Push, emoji, and related services. Prefer
the 4.5.x child in responses.

**4.5.1 Apple RSS vs scraping** *(guideline text; no rejection in this record)* — approved Apple
RSS feeds (such as iTunes Store RSS) are allowed. Scraping apple.com, the iTunes Store, App
Store, App Store Connect, the developer portal, or similar — or building rankings from scraped
data — is not.

**4.5.2 Apple Music / MusicKit** *(guideline text; no rejection in this record)* —

- Playback must be user-initiated with standard media controls. Do not require payment or
  indirectly monetize Apple Music access (IAP, ads, gating on user info). Do not download,
  upload, or share MusicKit-sourced files except as MusicKit docs allow.
- MusicKit is not a substitute for sync/adaptation licenses when you need a specific cue or
  shareable audio/video — clear rights with rights-holders. Cover art/metadata only with
  playback or playlists (including functional screenshots); no ads/marketing use without
  authorization. Follow Apple Music Identity Guidelines.
- Disclose playlist/favorites access in the purpose string. Do not share that data with third
  parties except to support/improve the app; no user/device identification or ad targeting.

**4.5.3 No spam via Apple services** *(guideline text; no rejection in this record)* — do not
use Apple services (Game Center, Push, Live Activities, etc.) to spam, phish, or send
unsolicited messages. Do not reverse-lookup, mine, or exploit Game Center Player IDs or aliases
— Program removal territory.

**4.5.4 Push Notifications discipline** *(guideline text; no rejection in this record)* — Push
must not be required for the app to function, and must not carry sensitive personal or
confidential information. Promotional or direct-marketing Push needs an explicit in-app opt-in
and an in-app opt-out. Abuse can revoke Push privileges. Pair with 5.1.2: do not gate core
features on enabling notifications.

**4.5.5 Game Center Player IDs** *(guideline text; no rejection in this record)* — use Player IDs
only as Game Center terms allow. Do not display them in the app or to third parties.

**4.5.6 Apple emoji** *(guideline text; no rejection in this record)* — Unicode that renders as
Apple emoji may appear in the app and metadata. Do not use Apple emoji on other platforms or
embed Apple emoji assets directly in the binary.

**4.6 Intentionally omitted** *(guideline text; no rejection in this record)* — Apple marks 4.6
as intentionally omitted. No active requirement.

**4.7 Mini apps, mini games, streaming games, chatbots, plug-ins, and game emulators**
*(guideline text; no rejection in this record)* — apps may offer software not embedded in the
binary: HTML5/JS mini apps and games, streaming games, chatbots, plug-ins, and (for retro
console / PC emulators) downloadable games. You are responsible for all of that software under
these Guidelines and applicable law. Non-compliant hosted software rejects the whole app.
Follow 4.7.1–4.7.5.

**4.7.1 Hosted software obligations** *(guideline text; no rejection in this record)* — offered
software must follow privacy rules including 5.1 (especially health and kids data); include
objectionable-content filtering, report + timely response, and block abusive users; and follow
3.1 for any digital goods or services.

**4.7.2 No exposing native APIs** *(guideline text; no rejection in this record)* — do not
extend or expose native platform APIs or technologies to hosted software without prior Apple
permission.

**4.7.3 Per-instance data consent** *(guideline text; no rejection in this record)* — do not
share data or privacy permissions with any individual hosted software title without explicit
user consent each time.

**4.7.4 Software index and universal links** *(guideline text; no rejection in this record)* —
provide an index of software and metadata in the app, including universal links to every
offered title.

**4.7.5 Age gating hosted software** *(guideline text; no rejection in this record)* — let users
identify software that exceeds the app's age rating, and restrict underage access with verified
or declared age.

**4.8 Login Services** *(guideline text; no rejection in this record)* — if the app uses a
third-party or social login to establish the user's primary account, it must also offer an
equivalent login option that limits collection to name and email, lets the user keep the email
address private, and does not collect in-app interactions for advertising without consent. Sign
in with Apple satisfies this; so does any service meeting those three properties.

No second option is required if the app uses only your own account system, is an education or
enterprise app requiring an existing institutional account, uses a government or industry
citizen ID, is an alternative app marketplace (or distributed from one) using marketplace
login, or is a client for one specific third-party service the user must sign into directly.

Practical read: adding "Sign in with Google" alone to an app that has its own accounts triggers
this. Either add Sign in with Apple alongside it or drop the third-party option.

**4.9 Apple Pay** *(guideline text; no rejection in this record)* — apps using Apple Pay must
show all material purchase information before sale and must use Apple Pay branding/UI correctly
per Apple Pay Marketing Guidelines and HIG. Recurring Apple Pay charges must disclose, at
minimum: renewal term length and that it continues until canceled; what each period includes;
actual charges; and how to cancel.

Wrong button art, missing price clarity, or silent recurring charges fail. Fix creatives and
the pre-charge sheet; test cancel instructions end to end.

**4.10 Monetizing built-in capabilities** *(guideline text; no rejection in this record)* — do
not monetize built-in hardware or OS capabilities (Push Notifications, camera, gyroscope, etc.)
or Apple services and technologies (Apple Music access, iCloud storage, Screen Time APIs, and
similar).

Charging solely to turn on the flashlight, send a push, or "unlock iCloud" fails. Charge for
your product value, not for Apple's primitive.

### Privacy and legal

Apps must comply with law everywhere you distribute. Soliciting criminal or clearly reckless
behavior rejects; extreme cases (trafficking, child exploitation) go to authorities. The numbers
below are Apple's floor — not a substitute for counsel.

**5.1 Privacy** *(guideline text; no rejection in this record)* — parent for personal data
handling under privacy best practices, applicable law, and the Developer Program License
Agreement. Prefer the 5.1.x child in responses. Customers expect care; shortcuts here remove
apps and accounts.

**5.1.1 Data collection, login walls, account deletion** — the operative rule, as stated by
the App Review Board:

> Apps cannot require user registration prior to allowing access to app content and features
> that are not associated specifically to the user.

Account-linked features sitting alongside the content — favorites, sync, progress tracking —
do not justify gating the content itself. Three things resolve it: registration optional for
non-account content, a clear statement that registering syncs across the user's devices, and a
way to register later. Arguing by analogy to large media apps does not work; those have
account-based purchased content.

Separately, 5.1.1(v) requires in-app account deletion for any app that creates accounts. This
gets rejected as missing even when it ships and even when the tap path is in the submission
notes. Put a row labeled exactly "Delete Account" at the top level of Settings, not nested,
reachable without contacting support, and make it delete rather than sign out or hide. If the
app stores a separate sensitive data set, ship a second delete for that set alone.

Also enforce from the current text:

- Privacy policy linked in App Store Connect **and** easily reachable in-app; it must name what
  is collected, how, all uses, third-party sharing with equal protection, retention/deletion,
  and how to revoke consent or request deletion.
- Consent before collecting user/usage data even if "anonymous"; paid features must not require
  that access; easy withdrawal; honest purpose strings. GDPR legitimate-interest paths must
  still satisfy that law.
- Minimize: only data for core tasks; prefer pickers/share sheets over full Photos/Contacts
  access.
- Respect permission denials; offer manual alternatives (for example typed address if Location
  is refused). Do not force unrelated permissions (microphone required just to post a photo).
- Social network login is not core unless the product *is* that network; provide access without
  that login or another mechanism; revoke credentials in-app; no storing social tokens off-device.
- Surreptitious password/private-data harvesting = Program removal.
- `SafariViewController` must be visible — not hidden — and not used to track without consent.
- Compiling personal information from sources other than the user without explicit consent
  (including public databases) is banned.
- Highly regulated fields (banking, healthcare, gambling, legal cannabis, air travel, crypto
  exchanges) or apps needing sensitive info should be submitted by a **legal entity**, not an
  individual; legal cannabis apps must be geo-restricted.
- Basic contact fields (name/email) may be requested only if optional, not gating features, and
  compliant with kids rules.

Elsewhere, 5.1.1 data-collection findings clear by making every collected field visibly power
a feature the user can see, and by moving login out of the first position in the flow.

**5.1.2 Data Use and Sharing, App Tracking Transparency** — "The app privacy information you
provided in App Store Connect indicates you collect data in order to track the user, including
Device ID. However, you do not use App Tracking Transparency to request the user's permission
before tracking their activity."

Know this catch-22 before it costs you a review cycle: App Privacy answers are app-scoped, not
version-scoped. If the live version declares `NSUserTrackingUsageDescription`, App Store
Connect may refuse to let you drop the corresponding data type, greying out Publish — so a
build that removed all tracking cannot declare accurate App Privacy. App Review's response to
this is typically a redirect to App Store Connect help. The App Review Board is the level that
resolves it.

**A tracking-removal build is high-risk for a spurious rejection on unrelated grounds.** Put
the removal front and center in the reviewer notes, naming the symbols and data types removed
and the version they were removed in.

Further 5.1.2 rules:

- Explicit permission before using/sharing personal data; disclose third-party AI sharing and
  get explicit permission; ATT before tracking; do not require Push, Location, or tracking to
  use the app or get compensation.
- No quiet repurposing of data for a new purpose without fresh consent (unless law allows).
- No surreptitious profiling; no re-identifying "anonymous" or aggregated Apple API data.
- Contacts/Photos/APIs are not for building a salable contact database or discovering which
  other apps are installed for ads/analytics.
- Contact people from Contacts/Photos only at the user's explicit individualized initiative —
  no Select All default; preview how the message will appear.
- HomeKit, HealthKit, Clinical Health Records, MovementDisorder, ClassKit, and depth/face
  mapping data (ARKit/Camera/Photo) may not feed marketing, advertising, or use-based mining
  (including via third parties).
- Apple Pay–acquired data only to third parties to facilitate/improve delivery of goods/services.

**5.1.3 Health and health research** *(guideline text; no rejection in this record)* — extra
rules for health, fitness, and medical data:

- Do not use or disclose health/fitness/medical-research data (Clinical Health Records,
  HealthKit, Motion and Fitness, MovementDisorder, human-subject research) to third parties for
  advertising, marketing, or use-based mining — except improving health management or health
  research **with permission**. You may use a user's health data to benefit that user directly
  (for example a premium discount) only if **you** are the entity providing the benefit and the
  data is not shared with a third party. Disclose which health data you collect from the device.
- Do not write false/inaccurate data into HealthKit or other medical/research apps; do not store
  personal health information in iCloud.
- Human-subject health research needs participant (or parental) consent covering nature,
  purpose, duration, procedures, risks/benefits, confidentiality/sharing, a contact point, and
  withdrawal.
- Human-subject health research also needs independent ethics review board approval; proof on
  request.

Pair with 1.4.1 medical disclaimers/sources. "Wellness tips" that quietly ship HealthKit to an
ad network fail here hard.

**5.1.4 Kids privacy** *(guideline text; no rejection in this record)* — COPPA, GDPR, and local
kids laws apply. Birthdate/parental contact may be requested only to comply with those statutes,
and the app must still offer useful function regardless of age. Apps intended primarily for kids
should not include third-party analytics or advertising (limited 1.3-style exceptions). Kids
Category apps, or any app that collects/transmits/shares personal information or persistent IDs
combined with personal data from a minor, need a privacy policy and must meet children's privacy
statutes. A Kids Category **parental gate is not** the same as verified parental consent to
collect data. "For Kids" / "For Children" metadata remains reserved to the Kids Category
(2.3.8). Cross-link 1.3.

**5.1.5 Location Services** *(guideline text; no rejection in this record)* — use Location only
when directly relevant to features. Do not use location APIs for emergency services or
autonomous control of vehicles/aircraft/devices except small drones/toys or remote car alarms.
Notify and obtain consent before collecting, transmitting, or using location; explain the
purpose in-app (see HIG). Pair with 4.5.4 / 5.1.2: do not gate the app on enabling Location.

**5.2 Intellectual Property** *(guideline text; no rejection in this record)* — only ship
content you created or licensed. Infringement can remove your app — and you can file when others
infringe you via Apple's claim form.

- **5.2.1** — no protected third-party material without permission; no misleading/copycat names
  or metadata; submit as the owner or licensee of the rights.
- **5.2.2 Third-party sites and services** — if the app uses, monetizes access to, or displays
  content from a third-party service, you must be permitted under that service's terms. Apple
  can demand proof. This kills scrapers and unofficial API clients.
- **5.2.3 Audio and video downloading** — no illegal file sharing; no save/convert/download from
  third-party sources (Apple Music, YouTube, SoundCloud, Vimeo, etc.) without explicit
  authorization. Streaming can also violate those terms — check first.
- **5.2.4 Apple endorsements** *(guideline text; no rejection in this record)* — do not suggest
  Apple is the source/supplier of the app or endorses your quality claims. If you earn Editor's
  Choice, Apple applies the badge; do not fake it.
- **5.2.5 Apple products** — no app confusingly similar to an Apple product, interface (Finder),
  app (App Store, iTunes Store, Messages), or ad theme. No Apple emoji in binaries/keyboards/
  stickers. iTunes/Apple Music previews are not free soundtrack/collage audio; if you show
  previews, link to the corresponding item. Activity rings must not mimic the Activity control's
  Move/Exercise/Stand presentation. WeatherKit data needs required attribution.

### Trademarks and IP complaints

Apple Legal complaints from trademark holders arrive as a separate channel from App Review and
escalate faster. The overwhelming trigger is a third-party trademark in the app name, subtitle,
or keyword field. Each notice carries the same warning:

> Developers with a history of allegations of repeat infringement, or those who misrepresent
> facts to Apple and/or the Complainant are at risk of termination from the Developer Program.

Hard rules:

- No third-party brand in the name, subtitle, or keywords. Not "for <Platform>," not as a
  keyword only. The keyword field is not private and keyword-only use draws complaints.
- No competitor app names as keywords, even within your own category.
- No third-party logos, art assets, or recognizable game art in screenshots or icons.
- When a complaint arrives: reply the same day, keep the reference number in the subject line,
  copy the complainant, and rename or pull the app. Fast capitulation resolves these in about a
  week. Arguing does not.

**5.3 Gaming, gambling, and lotteries** *(guideline text; no rejection in this record)* —
heavily regulated. Vet legal obligations in every storefront and expect longer review.

**5.3.1** *(guideline text; no rejection in this record)* — sweepstakes and contests must be
sponsored by the app's developer.

**5.3.2** *(guideline text; no rejection in this record)* — official rules for sweepstakes,
contests, and raffles must appear in the app and state clearly that Apple is not a sponsor or
involved.

**5.3.3** *(guideline text; no rejection in this record)* — do not use IAP to buy credit or
currency for real-money gaming of any kind.

**5.3.4** *(guideline text; no rejection in this record)* — real-money gaming (sports betting,
poker, casino, horse racing) or lotteries require licenses in every location served, must be
geo-restricted there, and must be **free** on the App Store. Illegal gambling aids (including
card counters) are banned. Lottery apps need consideration, chance, and a prize.

Betting-adjacent utilities that are not licensed gambling still draw false-positive 2.3.1 heat —
say plainly in notes what is *not* real-money wagering. True RMG without geo + license fails
5.3.4 regardless of notes.

**5.4 VPN apps** *(guideline text; no rejection in this record)* — VPN apps must use
`NEVPNManager`, and may only be offered by developers enrolled as an **organization**. Before
purchase or use, show an on-screen declaration of what user data is collected and how it is
used. May not sell, use, or disclose user data to third parties for any purpose — commit to that
in the privacy policy. Must not violate local law; if a territory requires a VPN license,
put license info in App Review notes. Parental-control, content-blocking, and security apps from
approved providers may also use `NEVPNManager`. Non-compliance means store removal, alternative-
distribution block, and possible Program removal.

**5.5 Mobile Device Management** *(guideline text; no rejection in this record)* — MDM apps must
request the capability from Apple. Only commercial enterprises, educational institutions,
government agencies, and (limited) parental-control or device-security companies. Before
purchase/use, declare on-screen what data is collected and how it is used. No selling/using/
disclosing data to third parties for any purpose — privacy policy must say so. Third-party
analytics only in limited cases that measure the MDM app's own performance — not the user,
device, or other apps. Configuration-profile apps follow the same rules. Non-compliance:
removal, install block, possible Program removal.

**5.6 Developer Code of Conduct** *(guideline text; no rejection in this record)* — the
account-level guideline, not an app-level one. It governs how you behave toward customers and
toward Apple, "including your responses in App Store Connect." Harassment, intimidation, and
repeated manipulative or misleading conduct lead to program removal rather than a rejection.

This is the clause to keep in mind while writing an angry Resolution Center reply. It also
covers "raise prices in a tricky manner, charge for features or content that are not delivered,
or engage in any other manipulative practices within or outside of the app."

Its four subsections:

- **5.6.1 App Store Reviews** — respond to customer reviews on topic, with no personal
  information, spam, or marketing. Use the system review-prompt API; custom prompts are
  disallowed.
- **5.6.2 Developer Identity** — your representation of yourself and your business must be
  accurate, current, and contactable.
- **5.6.3 Discovery Fraud** — manipulating charts, search, reviews, or referrals is prohibited.
  This includes paid, incentivized, filtered, or fake reviews, and hiring a third party to do it
  for you.
- **5.6.4 App Quality** — excessive negative reviews and excessive refund requests are named as
  evidence of a Code of Conduct problem. Support burden is a compliance surface, not just a
  business metric.

One useful detail: termination under this guideline has a documented path back. You may submit a
written statement detailing planned improvements, and if Apple approves the plan and confirms the
changes, the account may be restored.

## Pre-submission audit

Run in this order. Report findings before changing any code.

0. **Is this the app's first submission?** If so, cut scope to the core, pull all IAP, and check
   the reduced app still wins a feature comparison against the nearest competitor. See
   [Sequencing](#sequencing-what-to-ship-in-the-first-submission).
1. **Name, subtitle, keywords.** Search each for third-party brands, competitor app names, and
   platform names. Check the keyword field specifically — it is the most common miss. Anything
   trademarked is P0. Check every localization, not just the primary one: the rules apply per
   locale, and a second locale added for keyword surface gets scanned the same way.
2. **Screenshots, preview, icon.** Third-party logos or art, wrong device frames, low-res or
   jagged assets, crowded layouts, unreadable type. Screenshots must show real use. Previews
   must be portrait with no device frame.
3. **Differentiation, against 4.3.** If the app shares a codebase or feature set with others
   from the same account, name the specific features that make it distinct. If you cannot name
   any, say so plainly — that is a spam rejection waiting to happen.
4. **Substance, against 4.2.** Count what the app does against the nearest competitor and
   write the comparison into the reviewer notes.
5. **Hidden features, against 2.3.1.** Every feature visible and fully accessible during
   review. Audit remote config, feature flags, server-driven UI, and every third-party SDK.
6. **Payment routing, against 3.1.1 and 3.1.3(e).** Anything that unlocks digital content or
   functionality must use IAP — flag license keys, promo codes, QR unlocks, web-purchased
   entitlements, and crypto wallets. Conversely, anything consumed outside the app must *not*
   use IAP. If the app links out to a purchase page, confirm the storefront allows it: the US
   storefront does, others require a StoreKit External Purchase Link Entitlement.
7. **Subscription design, against 3.1.2.** Recurring value or static paywall? Real free tier?
   Restore visible? Period at least seven days, working on all the user's devices, with no task
   required to receive the paid value? If this is the app's first submission, pull the IAP out
   entirely and ship it as an update after approval.
8. **Paywall disclosure text.** All nine required items literally on screen, none clipped or
   behind a scroll. Call to action reads "Subscribe." Trial stated plainly.
9. **On-device name vs listing name vs all locales.** Exact match, per configuration. Confirm
   metadata holds to a 4+ rating and that "For Kids" appears nowhere unless the app is in the
   Kids Category.
10. **Regulated-category false positives.** For betting, gambling, alcohol, health, or finance,
    draft reviewer notes that state explicitly what the app does not do. Health apps also need
    the disclaimer on a first-launch screen and a visible source link inside every answer.
11. **Objectionable content, against 1.1 and 1.1.x.** Walk icon, screenshots, store copy, and
    the first-run experience for hate, porn, graphic violence, weapons commerce, religious bait,
    fake/prank device features, and tragedy monetization. If UGC or generative output can produce
    those, the 1.2 stack must catch them.
12. **Physical harm beyond medical, against 1.4.2–1.4.5.** Dosage calculators only from approved
    entities; no encouragement of tobacco, illegal drugs, or excessive alcohol; DUI data only from
    law enforcement; no IRL dares or distracted-driving prompts.
13. **Developer contact, against 1.5.** Support URL and in-app contact both work. Wallet passes
    carry valid issuer contact and correct signing if applicable.
14. **User-generated content, against 1.2.** If users can post anything, verify all four
    controls exist and are reachable: content filtering, a report mechanism with a real response
    process behind it, user blocking, and published contact information. Name each one's tap
    path in the reviewer notes.
15. **Kids Category, if selected, against 1.3 and 5.1.4.** Parental gate on any link out or
    purchase, no third-party analytics or advertising, no PII or device information to third
    parties, privacy policy present, verified parental consent handled separately from the gate.
16. **Remote code, against 2.5.2.** Confirm nothing downloads or executes code that changes
    features after review — no server-driven UI that alters reviewed behavior, no JavaScript
    bundle hot-updates. Configuration values are fine; new behavior is not.
17. **Account deletion, if the app creates accounts.** Top-level Settings row labeled "Delete
    Account," real deletion, plus a separate delete for any sensitive data set. Put the literal
    tap path in the notes — "Settings tab, Delete Account" — rather than describing it.
18. **Login wall, against 5.1.1.** Any content not specific to the user must be reachable
    without registering. Registration optional, sync explained, register-later path.
19. **Login services, against 4.8.** If the app offers any third-party or social login for the
    primary account, it must also offer an equivalent option limiting collection to name and
    email, supporting a private email address, and not tracking for ads. Check the exemption
    list before adding one.
20. **App Privacy and tracking, against 5.1.2.** App Privacy answers must match what the
    binary does. If the binary declares `NSUserTrackingUsageDescription`, App Privacy must list
    tracked data types and the app must call ATT before collecting. If tracking was removed,
    expect the version-scope catch-22, document the attempt with a screen recording, and say so
    in the notes. Confirm no feature or reward is gated behind enabling tracking, push, or
    location.
21. **Third-party data sources, against 5.2.2 and 5.2.3.** If the app displays, monetizes, or
    scrapes content from a service you do not own, confirm its terms permit it. Apple can demand
    proof. No media downloading or format conversion without authorization from the source.
22. **Credential-gated apps.** Verify the demo account yourself, then attach a screen recording
    of the login. If credentials cannot be shared for legal or security reasons, arrange a
    built-in demo mode with prior Apple approval instead. Confirm the backend is live and every
    configured IAP is reachable.
23. **Description wording and feature parity, against 2.3 and 2.1.** Flag any term a reviewer
    could ask you to define. Take every bullet in the description, release notes, and
    screenshots and name the exact screen and tap path that delivers it. A bullet without one
    is a rejection.
24. **Sign in with Apple button, if present.** `ASAuthorizationAppleIDButton`, style contrasting
    with its background.
25. **HealthKit or CareKit, if present.** Description names the Health app integration. Prefer
    one-time import over continuous upload. Pre-write the questionnaire answers into the notes.
26. **iPad at 2x.** Walk every screen in 2x compatibility mode on an iPad even for an
    iPhone-only app — reviewers commonly test there. Look for overlapping controls, clipped
    text, and hardcoded frames.
27. **Data security, against 1.6.** Confirm TLS in transit, no PII or tokens in logs, least
    privilege for third-party SDKs, and that App Privacy answers still match the binary.
28. **Crime-reporting apps, against 1.7.** Only if the app reports alleged crime: local law
    enforcement involvement is active in every storefront where the app is sold, with proof in
    Review Information.
29. **Reviewer notes.** Demo credentials, path to the core feature, IAP test steps, an
    explanation of every permission requested, the Delete Account tap path, a statement of what
    the app does not do if it is in a regulated category, and every change in this build
    described specifically — 2.3.1 requires specificity and rejects generic descriptions.
    Template in [TEMPLATES.md](TEMPLATES.md).

## When a rejection arrives

1. **Copy the reviewer's exact text out of Resolution Center and save it locally.** It
   disappears when the submission closes. Do this first, before any triage.
2. **If the citation is concrete, fix and resubmit.** Resubmission is faster than arguing.
3. **If the build is a bug fix, ask for it to be approved as-is.** Apple offers this at the
   bottom of the rejection and it is easy to miss: "If this is a bug fix submission and you'd
   like to have it approved at this time, reply to this message in Resolution Center to let us
   know. You do not need to resubmit your app for us to proceed."

   This is written policy, not reviewer discretion — the guidelines state under "After You
   Submit" that for apps already on the store, bug fixes will not be delayed over guideline
   violations. **One exclusion: violations related to legal or safety issues.** Those still block
   the build, so do not expect the shortcut for a 5.x or 1.x finding.
4. **Ask explicitly for a comprehensive review.** Otherwise the next round surfaces brand-new
   grounds. A two-line bug fix can draw four rejections in six days on four unrelated
   guidelines with nothing changing in the app between them.
5. **If the citation is vague, request a phone call.** It is the only channel that reliably
   produces real design feedback. Question list in [APPEALS.md](APPEALS.md).
6. **Get any concession in writing in Resolution Center.** Verbal does not survive.
7. **Appeal to the App Review Board when the rejection is factually wrong** about what the app
   contains, or when a platform bug makes compliance impossible, and the reviewer will not
   engage. Do not appeal a subjective quality judgment. Full decision rule and letter template
   in [APPEALS.md](APPEALS.md).
8. **Self-rejecting the binary restarts review from Waiting for Review.** Useful when a fix is
   ready before a reviewer picks the build up; costly otherwise.
