# ParryAI Shield

**Real-time prompt injection protection for ChatGPT, Claude, and Gemini.**

ParryAI Shield is a Chrome extension that inspects every AI chat prompt *before* it leaves your browser. If a prompt contains a known jailbreak pattern, a hidden instruction-override payload, leaked personal information, or other signs of prompt injection, the shield blocks the request and lets you know.

Detection runs **entirely on your device**. Your prompts never touch our servers.

- **Privacy policy:** [privacy.html](privacy.html)
- **Terms of service:** [terms.html](terms.html)
- **Developer:** Saido Labs LLC · Primary contributor: Jesse Crawford

---

## Table of contents

1. [Install](#install)
2. [How to use it](#how-to-use-it)
3. [What each UI element means](#what-each-ui-element-means)
4. [Settings](#settings)
5. [FAQ](#faq)
6. [Troubleshooting](#troubleshooting)
7. [Privacy & data handling](#privacy--data-handling)
8. [Report a bug / contact](#report-a-bug--contact)

---

## Install

### Option A — From the Chrome Web Store (recommended)

1. Go to the [ParryAI Shield listing](https://chromewebstore.google.com/) on the Chrome Web Store. *(link will be added here when the listing goes live)*
2. Click **Add to Chrome**, then **Add extension** in the confirmation dialog.
3. The ParryAI shield icon will appear in your Chrome toolbar. Pin it for easy access: click the puzzle-piece icon → pin **ParryAI — AI Security Shield**.

You are protected the moment the extension is installed. There is nothing to configure.

### Option B — Install the .zip manually (for reviewers / early access)

1. Download `parryai-extension.zip` from the release you received.
2. Unzip it to a folder you won't delete (e.g. `~/parryai-extension`).
3. In Chrome, go to `chrome://extensions`.
4. Turn on **Developer mode** in the top-right corner.
5. Click **Load unpacked** and select the folder you just unzipped.
6. The shield icon appears in the toolbar.

---

## How to use it

Once installed, **ParryAI works automatically on every visit to**:

- `chatgpt.com` and the legacy `chat.openai.com`
- `claude.ai`
- `gemini.google.com`

You do not need to sign in, paste an API key, or run any commands. Every time you hit Send on one of these sites, ParryAI inspects your prompt locally and one of three things happens:

| Outcome | What you see |
|---|---|
| **Clean prompt** | Nothing. The request goes through normally. The toolbar badge stays green. |
| **Suspicious prompt** | The request goes through, but the badge turns amber. Click the icon to see the scan result and the most recent threat category. |
| **Blocked prompt** | The AI provider receives a `403` instead of your prompt. The badge turns red with a `!`. The chat UI will typically show an error like "request failed — try again." Click the extension icon to see why it was blocked. |

Clicking the toolbar icon always opens the popup, which gives you:

- A live "Protected — N threats this session" status line.
- A 20-bar sparkline of your recent scan scores (session-only, cleared on browser close).
- A toggle to pause scanning temporarily (useful while debugging, rarely needed).
- A **View Details** button that opens the ParryAI dashboard in a new tab.

---

## What each UI element means

### Toolbar badge

| Badge | Meaning |
|---|---|
| No badge, green background | All recent prompts are clean. |
| Amber ⚠ | A medium-severity scan was logged recently. The request was allowed; review in the popup. |
| Red ! | A recent prompt was blocked, or a high/critical-severity detection fired. |

### Popup

- **ParryAI Shield** (header): quick-glance title and animated shield icon. The shield pulses red when the last detection was high or critical severity.
- **Status line** (color-coded): current session summary — "Protected" in green, "THREAT DETECTED" in red with the severity label.
- **Sparkline**: each bar is one recent scan. Bar height = threat score (0 to 1). Bar color matches the severity color (green = safe, amber = medium, red = high/critical). Empty slots are dim gray.
- **Scanning toggle**: pauses/resumes the shield globally. Your setting persists across browser restarts.
- **View Details**: opens [app.parryai.com/dashboard](https://app.parryai.com/dashboard) in a new tab.

### Settings page

Right-click the shield icon → **Options**, or open `chrome://extensions` → ParryAI → **Extension options**.

- **Block Threshold** (slider, 0.20 – 0.80): the minimum threat score required to block a request. Lower = more aggressive (more false positives, fewer misses). Default is 0.60.
- **Protected Providers**: independent on/off toggles for ChatGPT, Claude, and Gemini. Use this if one of the three AI services is misbehaving with ParryAI active and you don't want to disable protection on the others.
- **Privacy Policy** and **Terms of Service** links at the bottom open the bundled legal pages in a new tab.

---

## Settings

All settings are saved automatically to `chrome.storage.local` on your device. They never leave your browser.

| Setting | Default | What it does |
|---|---|---|
| Scanning enabled | On | Master switch. Off = the extension does nothing. |
| Block threshold | 0.60 | Numeric scan-score cutoff. Scores ≥ this value and flagged as threats are blocked. Critical-severity detections always block, regardless of this value. |
| ChatGPT scanning | On | Independent toggle for `chatgpt.com` / `chat.openai.com`. |
| Claude scanning | On | Independent toggle for `claude.ai`. |
| Gemini scanning | On | Independent toggle for `gemini.google.com`. |

---

## FAQ

### Does ParryAI slow down my AI sessions?

No. Detection runs synchronously on your device before the request is transmitted, typically in under 10 ms. If the scanner doesn't respond within 3 seconds (very rare), the shield fails open and the request goes through — you will never be stuck.

### Can ParryAI see my prompts?

Only the extension's own service worker, running inside your browser, sees the prompt text. It is passed to the local detection engine, scored, and immediately discarded. **Nothing is transmitted to Saido Labs, to us, or to any third party.** See the [Privacy Policy](privacy.html).

### What information is stored, if any?

Only scan result *metadata* — score, severity, category tag, source URL, and timestamp — is kept in `chrome.storage.session` (up to 200 entries, rolling). Chrome wipes `chrome.storage.session` when you close the browser. Settings live in `chrome.storage.local`. **No prompt text is ever persisted.**

### Does this work in Incognito / private windows?

Only if you explicitly allow it. Go to `chrome://extensions` → **ParryAI — AI Security Shield** → **Details** → enable **Allow in Incognito**.

### Does it work in Arc, Edge, Brave, Opera, and other Chromium browsers?

The extension is built for Manifest V3 and works in any current Chromium-based browser. However, only the Chrome Web Store listing is officially tested each release. If you load the `.zip` into a Chromium fork and hit an issue, please report it — most fixes are one-liners.

### Does it work in Firefox or Safari?

Not yet. Firefox support is on the roadmap once we've finished the Manifest V3 parity work for Gecko. Safari requires a native-app wrapper and is tracked separately.

### Why did it block / not block this specific prompt?

Each scan runs multiple detection layers (heuristic pattern matching, embedding similarity, canary-token tripwires, perplexity anomaly detection, behavioral rules, PII detection, steganography/hidden-text, and negative-space manipulation). The combined score is compared to your configured threshold.

If a legitimate prompt is consistently blocked, **raise the threshold** in options (for example 0.60 → 0.70). If a malicious-looking prompt slips through, **lower it** (for example 0.60 → 0.45). Critical-severity detections bypass the threshold entirely and always block.

### Does ParryAI ever send my data anywhere?

No. The extension makes **zero** outbound network requests. The popup's "View Details" button opens a web page in a new tab, but that is ordinary navigation — nothing is passed in the request. This will remain true for every release; any change to data handling would be a major-version bump and called out in release notes.

### Is there a Pro / paid version?

Not today. The extension is free and will stay free for individual use. Enterprise features (team dashboards, policy push, audit logs) will live in the `app.parryai.com` web product — but the extension itself does not require any account.

### Why do you need access to chatgpt.com, claude.ai, and gemini.google.com?

To inject the scanning script into those pages before you hit Send. These are the only hosts the extension has access to, and it does not read any data from them — it only inspects your own outgoing prompts inside your own browser. See the `host_permissions` entry in the extension manifest.

### What about api.openai.com, api.anthropic.com, or Vertex AI?

The current release only scans the web UIs. Direct API usage from your code will not be scanned. Server-side coverage is handled by the separate ParryAI server SDK.

### How do I uninstall?

`chrome://extensions` → **ParryAI — AI Security Shield** → **Remove**. All extension data is deleted at that moment.

---

## Troubleshooting

### The shield icon doesn't show up in the toolbar

Chrome hides new extensions behind the puzzle-piece icon by default. Click the puzzle piece, find **ParryAI — AI Security Shield**, and click the pin icon to pin it to the toolbar.

### The badge stays green but I know my prompt was risky

Two likely causes:

1. **Your threshold is too high.** Open the options page and drop the block threshold to 0.45 to see whether the scan is actually firing but the score is below cutoff.
2. **The prompt was sent to a provider you've disabled.** Check the per-provider toggles in options.

If the score is genuinely low on something that should be high, please file a report with the prompt pattern (sanitized) — that's feedback our detection engine learns from.

### ChatGPT / Claude / Gemini is broken — messages fail to send

First, check whether ParryAI is blocking them:

1. Click the shield icon. If the status line says "THREAT DETECTED", open the options page and **lower** the block threshold or temporarily disable the provider toggle.
2. If the sparkline is empty and messages still fail, **toggle scanning off** in the popup and retry. If messages succeed with scanning off, it's a detection false positive — please report the exact message text (redact anything sensitive).
3. If messages still fail with scanning off, it's not ParryAI — likely a change on the AI provider's side.

### I see "Request blocked by ParryAI" on every message, even harmless ones

This usually means the extension context was invalidated (Chrome just updated the extension). **Refresh the tab** and the protection re-initializes. If it keeps happening, disable-then-re-enable the extension at `chrome://extensions`.

### The options page is blank / won't open

Try closing the options tab and reopening it via `chrome://extensions` → ParryAI → **Extension options**. If it's still blank, the extension may have been installed into a read-only user profile; reinstall under a fresh profile.

### I installed from a .zip and Chrome says "This extension may have been corrupted"

That error means Chrome can't verify the extension's integrity. Either:

- The `.zip` was downloaded over a flaky connection — re-download.
- You extracted it with a tool that mangled paths (e.g. 7-zip on Windows with some options). Try the native Windows/Mac unzip, or install from the Chrome Web Store instead.

### The shield icon is red on pages I've never visited

The session-long threat timeline persists across all supported sites. If you opened Claude earlier and something fired there, the badge can remain red when you later open ChatGPT. Close and reopen the browser window to clear the session history, or just ignore it — the next clean prompt will turn it green again.

### Scanning is disabled after a Chrome restart

It shouldn't be — `scanningEnabled` lives in `chrome.storage.local` and persists. If you see this, please file a bug with your Chrome version and profile type (regular vs. enterprise).

### How do I completely wipe ParryAI's data?

`chrome://extensions` → ParryAI → **Remove**. All `storage.local` and `storage.session` data is deleted at that moment. Reinstalling starts from a clean slate.

---

## Privacy & data handling

- Prompt text is **never** persisted. It is passed to the local scanner and discarded after a score is produced.
- Scan metadata (score, severity, category, URL, timestamp) goes into `chrome.storage.session` — **cleared when you close Chrome**.
- Settings live in `chrome.storage.local` on your device.
- No analytics, telemetry, crash reports, or remote logging.
- No third-party libraries that phone home.
- The extension makes zero outbound network requests on its own.

Full policy: [privacy.html](privacy.html). Legal terms: [terms.html](terms.html).

---

## Report a bug / contact

- **General support:** [support@saidolabs.com](mailto:support@saidolabs.com)
- **Privacy questions:** [privacy@saidolabs.com](mailto:privacy@saidolabs.com)
- **Legal:** [legal@saidolabs.com](mailto:legal@saidolabs.com)
- **GitHub issues:** [file an issue](https://github.com/SaidoLabsLLC/parryaishield/issues) (please do not include any sensitive prompt content)

When reporting a detection bug, the most useful information is:

1. The site you were on (chatgpt.com / claude.ai / gemini.google.com).
2. What you were trying to do.
3. What the extension did (score + severity from the popup if available).
4. Your block threshold setting.
5. A **redacted** version of the prompt text — strip anything private before sharing.

---

*ParryAI — Detect · Deflect · Defend.*
