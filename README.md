# app-store-review-bot

An agent skill that audits an iOS app before you submit it to App Review, and tells you what
to do when a rejection comes back.

Apple publishes the App Store Review Guidelines. What they do not publish is the operational
layer: which guidelines actually fire on which app shapes, what the canned rejection text
means in practice, what fix clears it, and which rejections are worth arguing with. That is
what this skill encodes, drawn from patterns that recur across real rejections, appeals, and
App Review Board rulings.

## What is in it

| File | Contents |
|---|---|
| `SKILL.md` | What to ship in a first submission versus an update, a guideline hit list with Apple's canned reviewer language, a 29-step pre-submission audit, and an 8-step rejection response |
| `APPEALS.md` | When to appeal and when not to, escalation ladder, appeal letter template, phone-call question list |
| `TEMPLATES.md` | Reviewer notes template, paywall disclosure checklist, HealthKit questionnaire answers, subscription justification |

Every numbered App Store Review Guideline (1.x–5.x) has a completed entry in `SKILL.md` (observed or guideline-text).



**Current through Apple's June 8, 2026 guidelines update.** No copy of Apple's text is bundled
here — the skill carries the guidelines URL and tells the agent to fetch the live page for an
audit or a cited rejection, compare the "Last Updated" date, and flag entries that may have
drifted. Apple renumbers: two guideline numbers in an earlier draft of this skill had moved
underneath it, which is exactly the failure that design avoids.

Entries drawn from published guideline text rather than an observed rejection are marked as such,
so you can tell which advice carries verbatim reviewer language behind it.

## Installing

The skill is a directory of markdown. Copy or symlink it into whichever skills path your agent
reads.

```bash
# Cursor
ln -s "$PWD/app-store-review-bot" ~/.cursor/skills/app-store-review-bot

# Claude Code
ln -s "$PWD/app-store-review-bot" ~/.claude/skills/app-store-review-bot
```

## Using it

Ask for an audit before you submit:

> Audit this app against App Review before I submit. Check metadata, screenshots, the paywall,
> and App Privacy.

Or paste a rejection in:

> App Review rejected this citing Guideline 2.3.1. What do they mean and what do I do?

The skill is deliberately opinionated about two things. First, that some fraction of rejections
cite compliance the app already has, so the fix is usually visibility and reviewer notes rather
than code. Second, that appeals win on facts and lose on taste, so a design rejection should be
fixed and a factually wrong rejection should be escalated.

## Three things worth knowing even if you never install this

**Apple's rejection email contains no reason.** It is a pointer to Resolution Center. The
reason lives only there.

**Resolution Center threads are not archived.** They are reachable while the submission is in
Rejected or Unresolved Issues, then they are gone — not in the UI, not in the public API, not
in the internal API behind the web app. Copy every rejection message into your own notes the
day it arrives.

**A reviewer's verbal approval does not bind the next reviewer.** Get concessions in writing,
and even then do not rely on them.

## Scope

iOS and the App Store Review Guidelines. It does not cover Notarization Review, Mac App Store
specifics, or the Small Business Program.

It is documentation, not a linter. It reads your metadata and code and tells you what a
reviewer is likely to do with them; it does not run anything.

## Contributing

Rejection text is the valuable part. If you have canned reviewer language for a guideline not
covered here, or a fix that cleared a rejection, open a pull request with the guideline number,
the verbatim text with your app's identifying details removed, and what resolved it.

Please do not include app names, Apple IDs, reviewer names, phone numbers, ticket numbers, or
demo credentials.

## License

MIT. See [LICENSE](LICENSE).
