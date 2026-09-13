# Matatah — architecture (public overview)

This is a high-level overview for reviewers. It intentionally omits implementation
detail and secrets; the full source is private.

## One idea

Every client is a thin worker over one server-side **Apply Engine**. The engine
owns the queue, the state machine, the evidence rules, and "Mark Applied." No
client can mark a job applied without the engine seeing confirmation evidence.
That single rule is what makes "apply for real" trustworthy.

```
                        ┌───────────────────────────────┐
                        │  Apply Engine  (matatah.com)   │
                        │  queue · state machine ·       │
                        │  evidence rules · handoffs ·   │
                        │  Mark Applied (only on proof)  │
                        └───────────────┬───────────────┘
        ┌───────────────┬───────────────┼───────────────┬───────────────┐
    Chrome ext      Desktop app      Cloud session      Mobile         Web app
  (your Chrome)   (your machine)   (headless, streamed) (phone/iPad)  (matches, runs)
        └───────────────┴───────────────┴───────────────┴───────────────┘
                    one shared form-filler ("content.js")
```

## Find

A pool of 600k+ postings from 90k+ verified boards, refreshed by scheduled
scans across 30+ ATS families and company career pages. Descriptions are
embedded (pgvector) so matching is by meaning, not keywords. Every listing
tracks when it was posted and when first seen; recommendations blend the
best-fitting postings with the best-fitting *recent* ones. Applied or dismissed
roles — including reposts under a different title or on another board — never
come back.

## Write — the 10/10 packet

```
vault (your facts, each with an id)
  → classify the role family (a registry of seeds + families generated on demand)
  → profile the job description (must-haves, terms, company lines)
  → rank your evidence for THIS job (keywords + embeddings + family weights)
  → compose (the model may reorder and tighten; it cites the vault id behind
     every bullet and may not add a fact; employers/titles/dates are rendered
     from the vault, never written by the model)
  → QA gate  ──fail──▶ one retry, then legacy fallback (résumé) / not shipped (cover)
  → pass ▶ ship
```

The **cover letter** has its own dedicated pipeline and its own gate: a thesis
line, one company-specific insight that must build on a fact actually present on
the posting, exactly two proofs from the ranked evidence, level-matched voice,
and a near-duplicate check against recent letters for other companies.
Factuality is a hard block. A cover that fails is saved for the user to see but
**never attached** and **never replaced by a template**.

## Apply — success or a reason

```
queued → navigating → filling → submitting → confirmed → marked applied → next
                                   │
             needs you ◀──────────┤  captcha · login · code · a fact nobody has
             skipped   ◀──────────┤  closed · no form · email-only · wall not cleared
             failed    ◀──────────┘  submit never confirmed · form still rejecting
```

The queue never advances on "fields filled." A challenge (captcha, login,
verification code, or a required answer nobody has) pauses the run and is handed
to the human; the same application resumes after they clear it.

## Human hand-off, never evasion

Bot walls (Cloudflare Turnstile, hCaptcha, reCAPTCHA, and similar) reject the
*browser*, not the answers. Matatah never enters a challenge frame and never
tries to defeat one. Instead:

- The **extension** runs in your own Chrome, so most walls simply pass.
- A wall met by the **cloud** or **desktop** worker auto-routes the application
  to your own machine, carrying every answer already filled, where you clear the
  check from your real browser and IP and the run finishes.
- On **mobile**, a wall becomes "open in Safari/Chrome," you submit there, and
  confirm back in the app.

No CAPTCHA solving, no stealth/anti-detect browser, no fingerprint spoofing, no
residential/IP proxies, and the agent never types your password or OTP. The
guiding principle: move the work to the user's browser rather than moving the
user's identity to a bot.

## Clients

- **Chrome MV3 extension** — fills in the user's own browser.
- **Desktop app** — Electron; runs the queue on the user's machine with their
  logins; live view + take-over via a CDP screencast.
- **Cloud session** — the same worker in a headless browser on Render, streamed
  to the web app and phone over WebSocket; a fallback for sites without walls.
- **Mobile / iPad** — Expo/React Native; watch, take over, chat, manage the
  answer bank, and fill soft ATS forms in an in-app WebView.

All four run the **same** form-filler, so a fix lands everywhere at once.
