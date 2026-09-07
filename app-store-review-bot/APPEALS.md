# Appeals, Calls, and Escalation

How to escalate an App Review rejection, and when not to.

## The decision rule

Appeal when the rejection is **wrong about a fact** or when **Apple's own tooling makes
compliance impossible**. Do not appeal when the rejection is a **judgment about quality**.

| Rejection type | Appeal? | Why |
|---|---|---|
| Cites a feature the app does not contain | Yes | Verifiable, and the Board can check |
| Cites missing compliance the app already ships | Yes | Verifiable with a screen recording |
| Blocked by an App Store Connect bug | Yes | The reviewer cannot fix tooling; the Board can rule around it |
| Design quality, layout, typography (4.0) | No | Subjective; upheld on appeal |
| Spam and portfolio overlap (4.3) | No | Subjective; upheld on appeal |
| Minimum functionality (4.2) | No | Fix the app instead |

Observed asymmetry: appeals grounded in facts get overturned, sometimes in under a week.
Appeals grounded in disagreement about taste get upheld, and burn a week doing it.

A partial win is a real outcome. A Board ruling can clear the guideline you appealed while
upholding a different one found during the same review, so expect to fix something either way.

## Escalation ladder

Work down it in order. Each step is cheap and the next one is more expensive.

1. **Reply in Resolution Center asking for specifics.** Screenshots, the screen name, the
   exact feature. Sometimes this alone resolves it.
2. **Reply asking for a comprehensive review** so the next round does not invent new grounds.
3. **Request a phone call.** Often refused with a note that issues are resolved through written
   communication first. When granted, it is the highest-value channel available.
4. **Appeal to the App Review Board.** Use it once the reviewer is sending canned replies.
5. **Contact the developer contact form** for account-level matters that are not about one
   submission.

Two signals that mean stop replying and go to step 4 immediately:

- The replies have gone canned — the same paragraph twice, no engagement with your question.
- The reply escalates to a termination warning citing **Section 3.2(f)** of the Developer
  Program License Agreement, with no specifics attached. This is a template. It does not mean
  an investigation found something. Arguing with it in Resolution Center does not help.

## Timing expectations

From a real factually-wrong rejection that was overturned:

| Day | Event |
|---|---|
| 0 | Submitted, Waiting for Review |
| 1 | In Review, then Rejected the same morning |
| 1 | Reply asking for screenshots; escalation with termination threat the same evening |
| 2 | Phone call refused, told to appeal |
| 3 | Appeal submitted |
| 8 | Board puts it back In Review, and approves it 49 minutes later |

Seven days from rejection to live. Note the last line: the Board cleared the finding rather
than ordering a real re-review. If your appeal is factual and correct, the fix is
administrative once it reaches the right desk.

## Appeal letter template

Short, factual, no hostility. This structure won a fabricated hidden-functionality rejection:

```
We recently submitted an update (vX.Y) for our app <name> (<Apple ID>) that <what the
build changed>.

We received a rejection for Guideline <number>, saying <the exact accusation, quoted>. We
asked for screenshots and details, but the reviewer refused and sent canned messages asking us
to resubmit. We asked to speak to a reviewer on the phone and received the same canned
response.

Our app does not contain <the accused functionality>. It never has and never will. We have no
hidden functionality. We have been making apps for over <N> years. We respect the App Store
Guidelines and appreciate the work you do to keep the App Store safe. We would love to speak
to a reviewer and find out exactly what is going wrong here so we can get it fixed ASAP.
```

Why it works, element by element:

- **A flat factual denial** of the specific accusation, not a general defense of the app.
- **The record of asking for specifics and being refused.** This is the part that moves a
  reviewer's judgment out of the equation.
- **Tenure on the platform**, stated plainly and without leverage.
- **Explicit respect for the guidelines.** You are not challenging the rules.
- **A request to fix, not a demand to be let through.** You are asking for information.

What to leave out: comparisons to other apps that got away with it, accusations of bias,
revenue impact, deadlines, and any mention of the reviewer's competence.

## Platform-bug appeal

When the rejection stems from Apple's own tooling, the appeal is a different document. Include:

- The exact steps that fail, with a screen recording.
- Any Apple engineer statement from the developer forums confirming the behavior, quoted.
- Any Apple documentation that contradicts what App Review is asking for, quoted.
- What you did instead, and why the binary is compliant even though the declaration cannot be.

Do not expect the reviewer to acknowledge the bug. Two rounds of "please contact App Store
Connect help" is the normal response. The Board is the level that can rule around it.

## Phone call playbook

The call is the only channel that produces real design feedback, so do not waste it. Reviewers
will not answer these in writing, which is exactly why they are worth asking out loud.

For a vague design rejection:

- Where are images low resolution or jagged? Can you show an example?
- Where is it hard to read? Can you show an example?
- Where are screens crowded or difficult to complete tasks in? Can you show an example?
- What is the minimum font size?
- Is there a limit on buttons per screen?
- What is the minimum image resolution allowed?

For a spam or portfolio rejection:

- If we add features other apps do not provide, will we still be considered spam?
- Is our account flagged?
- Does this have to do with our other apps?
- Will we be able to continue updating this app after approval?

After the call:

1. **Write down what was said the same day.**
2. **Post a summary into Resolution Center** and ask the reviewer to confirm it in writing.
   Verbal statements do not carry to the next review — including "that rejection was a
   mistake," which has been followed by the same rejection being issued again days later.
3. **Ship exactly what was described**, and quote the call in the reviewer notes on
   resubmission. It may still be rejected. That is not a reason to skip the step.

## What a "more robust and unique user experience" turned out to mean

The stock phrasing for a quality rejection is content-free:

> As we discussed, we found several instances within your app that contribute to a lower
> quality user experience than Apple users expect. It would be appropriate to revise your app
> to provide a more robust and unique user experience in your resubmission.

The class of changes that answered it, generalized from a resubmission that cleared:

- Surface state the user was previously left guessing about — quota remaining, reset time, and
  a way to act on it, all visible on the main screen.
- Move configuration and filtering into onboarding instead of burying it in settings.
- Add a smart default with an explicit opt-out rather than an empty setting.
- Rebuild the primary interaction surface rather than restyling it.
- Raise asset resolution across the board.
- Add one substantial feature that is genuinely absent from comparable apps, and name it in the
  appeal as the differentiator.
- Ship a native tablet layout with tablet-appropriate type sizes.
- Update screenshots and description to match all of the above.

The pattern: depth the reviewer can see in the first minute, plus one nameable thing no
competitor has.
