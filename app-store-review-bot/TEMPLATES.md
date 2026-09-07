# Templates and Checklists

Fill-in text for the App Review Notes field, the paywall, and the questionnaires that arrive
with specific app capabilities.

## Reviewer notes

The notes field is the cheapest rejection insurance available. Most false-positive rejections
are things a reviewer could not find, would not look for, or assumed were absent.

```
Thanks for taking the time to review our app — we appreciate it. A GIF is attached for your
trouble.

WHAT CHANGED IN THIS BUILD
- <change 1>
- <change 2>

DEMO ACCOUNT
Username: <username>
Password: <password>
A screen recording of a successful login is attached.

HOW TO REACH THE CORE FEATURE
1. <tap path, step by step, from cold launch>

IN-APP PURCHASES
To test: <exact steps, including any state needed beforehand>
Restore purchases: <tap path>

PERMISSIONS AND WHY
- <permission>: <what breaks without it>

ACCOUNT DELETION
Settings tab > Delete Account. This permanently deletes the account and all associated data.
<If applicable> Settings tab > Delete <sensitive data set> deletes that data set alone.

WHAT THIS APP DOES NOT DO
- <the thing your category gets falsely accused of>
- No hidden features, remote configuration, or server-driven changes to reviewed behavior.

DESCRIPTION FEATURE MAP
- "<description bullet>" -> <screen name>, reachable via <tap path>
```

Notes on each section:

- **What changed** heads the list because it scopes the review. Without it, a two-line bug fix
  gets reviewed as a new app.
- **The tap path** must be literal. Describing where a feature lives has been rejected as
  missing; giving the taps has not.
- **What this app does not do** pre-empts the canned false positives — real money gambling for
  anything betting-adjacent, medical advice for anything health-adjacent, tracking for anything
  with an ad SDK history.
- **The description feature map** is the direct counter to a 2.3 metadata rejection. It also
  forces you to notice bullets you cannot map, which is the actual audit.

### Be a person

Attach a fun GIF, and open the notes with one friendly line thanking the reviewer.

App Review Information has an attachment slot, normally used for demo recordings and
supporting documents, and Resolution Center replies accept files too. A reviewer works through
a queue of terse, faceless submissions all day. Being the one that is pleasant to open costs
nothing and is the only lever you have on the human side of a process that is otherwise entirely
out of your hands.

Treat the effect as unmeasurable — no rejection outcome can be traced to it. Treat the downside
as near zero, with three limits: keep it work-safe and inoffensive, never let it substitute for
the substantive notes above, and skip it entirely when replying to a rejection that has turned
adversarial or cited a termination clause. At that point the tone to hold is flat and factual.

## Paywall disclosure checklist

Every item needs literal on-screen text on the purchase screen, per the Paid Apps Agreement.
Wording in the right column comes from a screen that passed review.

| Required disclosure | On-screen text |
|---|---|
| Title of publication or service | "The <Tier> Monthly Subscription offers <benefits> in the app" |
| Length of subscription | "with a subscription period of 30 days" |
| Price, and price per unit | "Subscribe for only $X.XX/month" |
| Payment charged at confirmation | "If you choose to purchase a subscription, payment will be charged to your iTunes Account" |
| Auto-renews unless turned off 24 hours before period end | "The subscription offered is an auto-renewing subscription…" |
| Renewal charged within 24 hours of period end, with cost | "and your account will be charged within 24 hours prior to the end of the current period" |
| User can manage and turn off auto-renewal in Account Settings | "Auto-renewal may be turned off at any time by going to your settings in the iTunes Store after purchase" |
| Links to Privacy Policy and Terms of Use | Large Terms of Service and Privacy Policy buttons at the bottom of the purchase screen |
| Unused free trial portion forfeited on purchase | "Any unused portion of a free trial period, if offered, will be forfeited when the user purchases a subscription to that publication, where applicable" |

Also check: call to action reads **"Subscribe,"** any free trial is stated plainly on
the purchase screen, and none of the disclosure text is clipped or hidden behind a scroll at
the smallest supported size.

## Clinical Health Records questionnaire

Arrives as a 2.1 Information Needed on any HealthKit or CareKit submission. Answer every part
in one reply; partial answers restart the loop. The questions, verbatim:

> - Please explain in detail how your app integrates with the Clinical Health Records API.
> Where is the user's information uploaded? At what frequency?
> - What features of your app require user information? How is the feature enabled? How can it
> be turned off?
> - How else is the information utilized?
> - Where is the information stored? Who has access to it?

A reply shape that cleared it:

- **How it integrates.** Receives <record type> from the user's medical records, used to
  <the single concrete thing it powers>.
- **Where uploaded, how often.** A HIPAA-compliant encrypted database controlled and managed by
  the developer. Uploaded once, at the time of the user's initial import. Updating requires the
  user to import again from the Health app.
- **Which features require it.** None. The integration only automates steps already available
  manually.
- **How enabled and disabled.** Enabled by <tap path>. One-time import. All imported data is
  deletable at any time via <tap path>. The entire account and all associated data is deletable
  via Settings, Delete Account.
- **How else used, who has access.** Only for the features above. No other party has access to
  the database.

The five properties of a passing answer: the data is **optional**, it only **automates
something the user could do by hand**, upload is **one-time rather than continuous**, storage
is **named and encrypted**, and there is a **visible in-app delete** for both the health data
alone and the whole account.

Ship both delete controls before submitting, not after being asked.

## Health app integration disclosure

Required by 2.5.1 whenever HealthKit or CareKit is linked. Add to the App Store description:

```
Health app integration (optional) - <App> integrates with the Health app to import your
<data types>. <App> will automatically <what it does with them>. This is not required and is
completely optional.
```

## Subscription justification

For a 3.1.2 rejection asking why a subscription is auto-renewing. The argument that works ties
recurring cost to recurring value:

```
<App> provides <ongoing service — alerting, monitoring, a content pipeline, sync> that runs
continuously on our servers and carries an ongoing cost per user. A one-time price cannot fund
a recurring cost structure.

Subscribers receive: <the recurring benefits, listed>.

Non-subscribers receive <the metered free tier> at no cost, every month, with no account
required.
```

Have a real free tier before making this argument. A metered free tier — a fixed number of
units per month for everyone — has passed where a trial-only model did not.

## Tracking-removal build

For any build that removes an ad SDK, `NSUserTrackingUsageDescription`, or device ID
collection. These builds draw spurious rejections on unrelated grounds, so lead with it:

```
Version <old> included NSUserTrackingUsageDescription and <SDK name>. This version has
completely removed all device ID collection, NSUserTrackingUsageDescription, and all tracking
code and SDKs.

<If App Privacy cannot be updated> App Store Connect does not currently permit removing
<data type> from App Privacy while the live version declares it. A screen recording of the
attempt is attached. The binary submitted here collects none of it.
```
