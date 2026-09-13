# Matatah — LOCK IN Hack submission

**Team #159 · Revanth Kondaveti**

Matatah finds the jobs that fit you, writes a résumé and cover letter that are 10/10 for each specific role — behind a factuality QA gate that refuses to ship a fabricated or generic packet — and then **applies for real** across a shared Apply Engine (Chrome extension, desktop app, cloud, and mobile), stopping to hand you the wheel for captchas, logins and questions only you can answer. It never bypasses a security check.

- **Live demo:** https://matatah.com
- **Presentation:** [`deck/Hakuna-Matatah.pptx`](deck/Hakuna-Matatah.pptx) (16 slides, includes a 2-minute animated intro and a real engine screen-recording)
- **Screenshots:** [`screenshots/`](screenshots/)
- **Architecture:** [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md)

## Walkthrough (narrated, ~74s)

Sign-in → dashboard → matches → evaluations → Bot Apply → a real form being filled, paused for a human answer, and submitted — with a voiceover.

https://github.com/revanthvne/matatah-lockinhack/raw/main/matatah-walkthrough.mp4

<video src="https://github.com/revanthvne/matatah-lockinhack/raw/main/matatah-walkthrough.mp4" controls width="100%"></video>

▶ If the player above doesn't load, [download / view `matatah-walkthrough.mp4`](matatah-walkthrough.mp4).

---

## ⚠️ Pre-existing work disclosure

**Matatah is a pre-existing project.** The full application lives in a private repository (`revanthvne/matatah`) and was in active development before this hackathon. This is disclosed here and in the submission form per the event rules.

**This repository is a submission companion, not the product's source.** It contains documentation, architecture, the presentation, and screenshots so judges can review the project. The core application code (the form-filling engine, the tailoring/QA pipeline, the Apply Engine, and the clients) remains **private** to protect proprietary work. Judges who need to inspect the source can be granted read access to the private repo on request.

**What was built during the hackathon window:**

- **Mobile / iPad Bot Apply** — the phone as a first-class worker: queue + answer bank + status in-app, an in-app WebView that fills "soft" ATS forms with the same engine as the extension, and an honest hand-off to Safari/Chrome (or the desktop) when a site shows a bot wall.
- **Cover 10/10 pipeline** — a dedicated cover-letter generator behind a hard factuality gate: a thesis line, one company-specific insight grounded in a fact actually found on the posting, exactly two proofs drawn from the candidate's real record, level-matched voice, and a near-duplicate check across recent letters. A letter that fails the gate is never shipped (no template fallback). Verified on a golden set of 3 personas × 3 job descriptions: rubric p50 10/10, factuality 100%.
- **Honest routing** — the cloud agent yields to the user's own machine (extension/desktop) whenever it's online, and a bot wall met by the cloud auto-routes the application to the user's real browser with the answers carried over. No IP proxying, geolocation matching, or fingerprint spoofing.
- **Human-cadence typing** — short fields are typed key-by-key at a randomized human pace; nothing about the browser is spoofed.

---

## The problem

Job seekers lose on three fronts at once: they can't see which of the hundreds of thousands of open postings actually fit them, every résumé/cover they send reads like everyone else's, and each application is ~20 minutes of repetitive form-filling. Matatah attacks all three.

## What it does

**Find.** Reads 600,000+ live postings from 90,000+ verified career boards (Greenhouse, Lever, Ashby, Workday, iCIMS, SmartRecruiters and 30+ more ATS families, plus company pages), embeds descriptions, and ranks by meaning against your profile — blending in fresh postings, because the first days of a listing convert best.

**Write.** A résumé and cover letter tailored to *this* role: a one-line thesis about you, the proof that matters to this reader, nothing invented. An 8-check QA gate (ATS-safe layout, truth vs. your record, thesis specificity, proof ranking, level-appropriate language, company specificity, differentiation, polish) must pass before a packet can ship.

**Apply for real.** Continuous mode fills every step — including multi-page Workday flows — presses Submit, and waits for the confirmation before marking Applied. "Fields filled" is never an outcome: it's success (with evidence), or a reason.

**Hand off, never sneak past.** Captchas, logins, verification codes, and questions only you can answer pause the run and hand you the wheel; you clear them in your own browser, and the same application continues. No captcha solving, no stealth browsers, no proxy tricks.

## Tech stack

Next.js 16 (App Router, TypeScript) on Vercel · Postgres on Neon with pgvector · Drizzle ORM · Claude (Anthropic) for scoring/tailoring/agent passes + OpenAI embeddings · Chrome MV3 extension · Electron desktop app (Playwright + CDP screencast) · Node + Playwright cloud sessions on Render · Expo / React Native mobile · Stripe · Gmail API.

## Safety stance

Matatah is built to help a person through redundant applications, not to defeat the sites it visits. It does not solve or bypass CAPTCHAs, does not use stealth/anti-detect browsers, fingerprint spoofing, or residential/IP proxies, and never types a user's password or one-time code as the agent. Security checks are always human moments. The design principle is simple: don't move the user's identity to a bot — move the bot's work to the user's own browser.

## License

All rights reserved. See [LICENSE](LICENSE). This companion repository may be read by hackathon judges for evaluation; the software itself is not open-source.
