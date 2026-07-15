# Tell

**A client-side phishing examiner.** Paste a suspicious email and Tell shows you exactly what gives it away — not just a verdict, but the reasoning behind it.

🔗 **Live:** [i-amour.github.io/tell](https://i-amour.github.io/tell)

> Add a screenshot here once it's deployed — a picture of the annotated report earns more attention than any paragraph. Save it as `screenshot.png` in the repo and it'll render below.
>
> `![Tell analysing a sample phishing email](screenshot.png)`

---

## Why I built it

Most "is this phishing?" tools give you a yes/no and ask you to trust it. That's the wrong shape for a security tool — the useful part is *learning to see the tells yourself*, so the next suspicious email doesn't need a tool at all.

Tell is built around that idea. It runs a transparent set of checks against an email, scores the result, and then annotates the message in place so you can see precisely which words, links, and headers triggered each flag. The whole thing runs in the browser: paste an email you're worried about and nothing is uploaded, logged, or sent anywhere.

## How it works

Tell is a rules-based classifier, not a black box. Each check looks for a specific, well-documented phishing trait and, if it fires, produces a **finding** with three things: a severity, a plain-English explanation of *why it matters*, and the exact snippet from the email that caused it.

Findings are weighted and summed into a single risk score:

| Severity | Weight | Examples |
|----------|--------|----------|
| High | 34 | Credential requests, sender impersonation, link text that hides its real destination |
| Medium | 16 | Generic greetings, suspicious TLDs, mismatched reply-to, reward bait |
| Low | 8 | Weaker supporting signals |

The score is capped at 100 and mapped to a verdict:

- **0–39 — Looks clear**
- **40–69 — Suspicious**
- **70–100 — Likely phishing**

Weighting rather than a simple count matters: one "the link says paypal.com but points to an IP address" should outweigh three soft stylistic signals. The weights are deliberately visible in the code so the logic can be argued with and tuned.

## What it looks for

**Language & pressure**
- Manufactured urgency ("within 24 hours", "your account will be suspended")
- Requests to verify or confirm passwords, cards, or bank details
- Generic, impersonal greetings ("Dear Customer")
- Too-good-to-be-true rewards and threats of consequence

**Links**
- Raw IP-address destinations
- Punycode / look-alike (`xn--`) domains
- URL shorteners that hide the real target
- Cheap, abuse-heavy top-level domains
- A trusted brand name buried in a longer domain (`paypal.com.secure-login.ru`)
- Anchor text whose visible domain doesn't match the actual `href`

**Headers**
- Sender display name impersonating a brand it isn't sent from
- Look-alike characters in the sender name (`PayPaI`, `amaz0n`)
- A `Reply-To` pointing at a different domain than the sender

**Attachments**
- Mentions of archive, executable, or macro-enabled file types

## Why it runs entirely in the browser

Phishing emails are, by definition, things people are already nervous about. Asking them to paste that email into a server they don't control is a poor trade. Every check in Tell runs locally in vanilla JavaScript with **no network calls** — no API, no analytics, no storage. That constraint shaped the design: it's why the detection is heuristic rather than an ML model behind an endpoint, and it's a feature worth stating plainly.

## Honest limitations

This is a learning-and-triage aid, not a security guarantee.

- **Heuristics miss things.** A well-crafted, targeted email that avoids the obvious tells can score low. A clean bill from Tell is not proof an email is safe.
- **False positives happen.** Legitimate marketing emails use urgency and shorteners too. The thresholds are tuned to be a bit cautious on purpose.
- **It reads what you paste.** With full headers (including `From` and `Reply-To`) it can do far more than with body text alone.
- **It doesn't follow links or resolve domains** — that would require the network calls the privacy model rules out.

When something matters, the right move is still the boring one: don't click, and check with the sender through a channel you already trust.

## Run it locally

It's a single self-contained file — no build step, no dependencies.

```bash
git clone https://github.com/I-amour/tell.git
cd tell
open index.html    # or just double-click it
```

## Deploy on GitHub Pages

1. Push `index.html` to the `main` branch.
2. **Settings → Pages → Source: `main`**, save.
3. It goes live at `https://<username>.github.io/tell` within a minute.

## Possible next steps

- A small labelled test set to measure precision/recall and tune the weights against real data
- Optional domain-age / DNS checks as a clearly-flagged "online" mode users opt into
- A browser-extension version that examines the email you're currently reading

## Built with

Vanilla JavaScript, HTML, and CSS — no framework. Type set in Clash Display, General Sans, and JetBrains Mono.

**Author:** Simi Olusola · [github.com/I-amour](https://github.com/I-amour)
