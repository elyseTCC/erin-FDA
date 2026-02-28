# FDA Advocacy Tool — Project Plan & Progress

## Project Goal
Build a user-friendly web tool that empowers Erin Hoops' supporters to send personalized advocacy emails to FDA leadership and their own elected representatives, urging approval of Troriluzole for Spinocerebellar Ataxia Type 3 (SCA3).

---

## Current Status: ✅ Phase 8 Complete — Email Body Fix

---

## Files Created

| File | Description | Status |
|------|-------------|--------|
| `instructionsFDA.md` | Saved instructions/spec for the tool | ✅ Done |
| `erin-fda-email-tool.html` | Fully functional advocacy web tool (v8) | ✅ Done |
| `FDAplan.md` | This file — project plan and progress tracker | ✅ Done |

---

## How the Tool Works (User Flow)

1. **User opens** `erin-fda-email-tool.html` in any web browser (no install needed)
2. **User reads** the welcome message and Erin's story
3. **User fills in:**
   - Their full name *(required)*
   - Their zip code *(required)*
   - Relationship to Erin (optional, e.g. "friend", "colleague")
   - Their phone number (optional)
   - **✨ NEW: A personal message/story** (optional — typed directly in the tool)
4. **Tool auto-looks up** U.S. Senators and House Representative based on zip code
5. **Tool displays** the representatives found with their emails
6. **Tool generates** a pre-filled email preview:
   - Yellow highlights = placeholders to fill in
   - Green highlight = their personal message
7. **User clicks** "Open in My Email App & Send"
8. **User's own email app opens** with everything pre-filled
9. **User personalizes** highlighted sections and hits Send — from their own email address
10. **✨ NEW: Thank you confirmation** appears with animated checkmark
11. **✨ NEW: Action counter increments** to show community momentum
12. **User is prompted to share** via Facebook, X/Twitter, WhatsApp, Email, or Copy Link

> ⚠️ **Important:** The *user* is the one sending the email — not the tool creator. Each person who visits the page sends from their own email address. This makes the campaign more powerful because the FDA and congressional offices receive many unique, personal emails from real constituents.

---

## Email Configuration

| Field | Value |
|-------|-------|
| **To** | commissioner@fda.hhs.gov |
| **Cc (FDA)** | steven.kozlowski@fda.hhs.gov |
| | kaveeta.vasisht@fda.hhs.gov |
| | TracyBeth.Hoeg@fda.hhs.gov |
| | vinayak.Prasad@fda.hhs.gov |
| | druginfo@fda.hhs.gov |
| **Cc (Auto)** | User's Senators + House Rep based on zip code |
| **Subject** | Urgent: Approve Troriluzole with Regulatory Flexibility |
| **Body** | Full advocacy letter with personalized placeholders + user's personal message |

---

## Phase 1 Features ✅

- ✅ Beautiful, warm, professional design (Playfair Display + Source Serif 4 fonts)
- ✅ Erin's story prominently displayed with pull quote
- ✅ 4-step visual guide so users know what to do
- ✅ Auto zip code → congressional representative lookup (Google Civic API)
- ✅ Displays representative names, roles, and emails
- ✅ Full email preview before sending (with highlighted placeholders)
- ✅ mailto: link opens user's default email app pre-filled
- ✅ Works in any browser, no installation, no backend server needed
- ✅ Mobile responsive
- ✅ Fallback notice if API lookup fails

## Phase 2 Features ✅ (NEW)

- ✅ **Personal Story Text Box** — Users type their personal message directly in the tool; it appears highlighted in green in the email preview and is injected into the email body automatically
  - 1,000 character limit with live character counter
  - Optional — users can leave blank to remove that section
- ✅ **Action Counter** — Animated number showing how many people have taken action
  - Stored in browser localStorage (persists across visits on the same device)
  - Animates smoothly when the page loads and when a user sends an email
  - Displayed in a prominent banner below the hero
- ✅ **Thank You / Confirmation Page** — Appears automatically after user clicks Send
  - Animated green checkmark
  - Encouraging message and call to share
- ✅ **Social Sharing Buttons** — Full suite of share options:
  - 📘 Facebook
  - 𝕏 X / Twitter
  - 💬 WhatsApp
  - ✉️ Email a Friend
  - 🔗 Copy Link (with confirmation message)
  - All buttons use the page URL + pre-written share text

---

## Sharing the Tool

The HTML file can be shared by:
- **Hosting it on a website** (GitHub Pages, Squarespace, Wix, Webflow, etc.)
- **Emailing the file** directly to supporters
- **Posting a download link** on social media
- **Embedding** in an existing webpage

---

## Phase 3 Changes ✅ (LATEST)

- ✅ **Hero text updated** — Now reads: *"Thank you so much for helping Erin, the Spinocerebellar Ataxia and Rare Disease communities. Please enter your information below to generate an email that will go to FDA Leadership and your representatives."*
- ✅ **"Erin's Story" card removed** — Replaced with a new informational card
- ✅ **SCA Disease Info card added** — Explains what spinocerebellar ataxia is, symptoms, subtypes, long-term prognosis, and why treatment urgency matters. Key points covered:
  - 40+ genetic subtypes affecting ~150,000 Americans
  - Symptoms: difficulty walking, impaired balance, slurred speech, swallowing problems
  - Progressive loss of mobility; many patients require wheelchairs
  - No FDA-approved therapy or cure for any subtype
  - SCA3 (most common form) is typically fatal, often in patients' 50s–60s
  - Brain cannot regenerate — every delay means permanent, irreversible decline

---

## Phase 4 Bug Fix ✅


**Issue:** `Uncaught ReferenceError: lookupReps is not defined` when pressing the preview email button.

**Root Cause:** When the HTML file was served through certain environments (including Claude's preview interface), Cloudflare injected its own `<script data-cfasync="false" src="...">` tag directly in front of the page's main `<script>` block. This split the script context, causing all functions to be defined in an inaccessible scope — invisible to the browser's global namespace. The file was also found to be truncated (the `copyLink` function and closing HTML tags were cut off).

**Fixes Applied:**
- ✅ Removed all `onclick=` inline attributes from HTML buttons — these are fragile when scripts load in unexpected execution contexts
- ✅ Rebuilt the entire JavaScript section cleanly from scratch
- ✅ Explicitly attached all functions to the `window` object (e.g. `window.lookupReps = lookupReps`) so they are always globally accessible regardless of how or where the script loads
- ✅ Added `DOMContentLoaded` event listeners as a second layer of protection — all buttons wire up their click handlers once the page finishes loading
- ✅ Rewrote template literals as standard string concatenation for maximum compatibility across browsers and script environments
- ✅ Fixed truncated file — `copyLink` function and all closing `</script>`, `</body>`, `</html>` tags restored

---


## Phase 5 Bug Fix ✅

**Issue:** Clicking "Open in My Email App & Send" caused the screen to go blank. The default email app did not open and no pre-filled email appeared.

**Root Cause:** The full email body, when URL-encoded and appended to a `mailto:` string, exceeded browser URL length limits (~2,000 characters). When `window.location.href` is set to an oversized `mailto:` URL, most browsers navigate to a blank page instead of launching the email client.

**Fixes Applied:**
- ✅ **Replaced `window.location.href` with a hidden `<a>` element** — A hidden anchor tag is created programmatically, its `href` set to the `mailto:` URL, then `.click()` is called. This launches the email client without navigating away from the page, so users never see a blank screen.
- ✅ **Body length capped at 1,500 characters** for the mailto link — The full letter text always remains visible in the on-screen email preview. If the body is trimmed, a note is included directing the user to copy the rest from the preview above.
- ✅ **Applied same anchor-click fix to "Email a Friend" share button** — `shareViaEmail()` was also using `window.location.href` and received the same treatment.
- ✅ **Fixed recurring file truncation** — The file was being cut off at the end of the `DOMContentLoaded` block on each save cycle; closing lines (`copyLink` listener, `});`, `</script>`, `</body>`, `</html>`) are now appended correctly.


## Phase 6 Changes ✅

**Changes Requested:**
- Rename "Find My Representatives" button to just "Preview Email"
- Button should act as a direct mailto link — clicking it opens the user's default email app (Gmail, Outlook, Apple Mail, etc.) with the email pre-filled and ready to send
- Remove the on-page email preview entirely — users should not see the email body on the webpage
- Fix the blank screen issue when the button is clicked

**What Was Done:**
- ✅ **Entire tool rebuilt from scratch** with a simplified, clean flow
- ✅ **"Preview Email" button** — single gold CTA button; clicking it:
  1. Validates name and zip code
  2. Looks up congressional representatives via Google Civic API
  3. Fires a hidden `<a>` mailto link that opens the user's default email app (Gmail, Outlook, Apple Mail, etc.) with To, CC, Subject, and full body pre-filled
  4. Shows a thank-you confirmation on the page
  5. Increments the action counter
- ✅ **No on-page email preview** — the email body is built in JavaScript and sent directly to the email client; nothing is displayed on the webpage
- ✅ **Blank screen bug fully eliminated** — replaced `window.location.href = "mailto:..."` with a hidden `<a>` element that is programmatically clicked; the page never navigates away
- ✅ **"Try Again" link** in thank-you section re-fires the same mailto if the email app didn't open
- ✅ **Fallback** if rep lookup API fails — email still opens with just FDA contacts in CC
- ✅ **Personal message** (if entered) is appended to the email body automatically
- ✅ Updated steps from 4 → cleaner: Fill in info → Click Preview Email → Email app opens pre-filled → Review & Send

**New User Flow:**
1. User fills in name, zip code, optional relationship/phone/personal message
2. Clicks **"✉️ Preview Email"**
3. Tool looks up their senators + House rep by zip code (takes ~1 second)
4. Default email app opens with full pre-filled email (To, CC, Subject, Body all set)
5. User reviews and clicks Send in their email app
6. Thank-you message appears on the page


## Phase 7 Changes ✅

- ✅ **BCC added to all generated emails** — eehoops@gmail.com is automatically BCC'd on every email sent, so Erin receives a copy of every message that goes to the FDA and representatives
- ✅ **X / Twitter share button removed** from the "Spread the Word" section
- ✅ **Bluesky share button added** — opens Bluesky's compose intent with pre-written share text and page URL
- ✅ **Instagram share button added** — because Instagram does not support direct share links, clicking it copies the page URL to the clipboard and shows a prompt guiding the user to paste it into an Instagram post or story caption

---


## Phase 8 Fix ✅ (LATEST)

**Issue:** Clicking "Preview Email" opened the user's email app but To, CC, BCC, Subject, and body were NOT auto-filled.

**Root Cause — URL Length Limit:**
The full email body is ~4,500 characters of plain text. When URL-encoded, this becomes ~6,200 characters. The complete mailto URL was **6,587 characters total** — far exceeding the browser limit of ~2,000 characters. When a mailto URL is too long, browsers silently open a blank compose window with nothing filled in, which is exactly what was happening.

**Solution — Clipboard + Mailto Split:**
It is technically impossible to reliably deliver a 4,500-character email body through a mailto URL across all browsers and email clients. The fix uses a two-step approach:

1. **The email body is automatically copied to the clipboard** the moment the user clicks "Preview Email" (using the Clipboard API with a textarea fallback for older browsers)
2. **The mailto link opens with only To, CC, BCC, and Subject** — these are short enough to stay well under the URL limit and fill in reliably every time
3. **A green instruction box appears** telling the user their email app is open and guiding them to click in the body area and press Ctrl+V (Windows) or Cmd+V (Mac) to paste
4. **The full email body is displayed** at the bottom of the thank-you section for reference, with a "Copy Email Body" button so users can re-copy if needed
5. **A retry link** re-fires the mailto if the email app didn't open on the first click

**What is reliably pre-filled in the email app:**
- ✅ To: commissioner@fda.hhs.gov
- ✅ CC: All 5 FDA contacts + user's senators and House rep
- ✅ BCC: eehoops@gmail.com
- ✅ Subject: Urgent: Approve Troriluzole with Regulatory Flexibility
- ✅ Body: Copied to clipboard — user pastes with one keystroke

---

## Potential Future Enhancements

- [ ] Host on a dedicated domain (e.g. helperin.org or approvetroriluzole.com)
- [ ] Use a backend (simple Google Form or Airtable) to track real-time action count across all users/devices
- [ ] Add a photo or short video of Erin to make the story more personal
- [ ] Translate into Spanish and other languages for broader reach
- [ ] Add a phone call script option (calling congressional offices is often even more effective than email)
- [ ] Add a "Wall of Support" section showing recent signers (first name + city only)
- [ ] Build a shareable progress bar toward a goal (e.g. "500 emails sent!")
- [ ] Add a QR code so the tool can be shared at in-person events

---

## Key People & Contact Info

| Person | Role | Contact |
|--------|------|---------|
| Erin Hoops | Patient — SCA3 | 925-963-7087 / eehoops@gmail.com |
| Martin Makary, M.D., M.P.H. | FDA Commissioner | commissioner@fda.hhs.gov |
| Steven Kozlowski, M.D. | Chief Scientist | steven.kozlowski@fda.hhs.gov |
| Kaveeta Vasisht, M.D., Pharm.D. | Assoc. Commissioner, Women's Health | kaveeta.vasisht@fda.hhs.gov |
| Tracy Beth Høeg, M.D., Ph.D. | Acting Director, CDER | TracyBeth.Hoeg@fda.hhs.gov |
| Vinay Prasad, M.D., M.P.H. | Chief Medical & Scientific Officer / CBER Director | vinayak.Prasad@fda.hhs.gov |
| FDA Drug Info | General Drug Inquiries | druginfo@fda.hhs.gov |

---

## Drug Background

- **Drug:** Troriluzole
- **Condition:** Spinocerebellar Ataxia Type 3 (SCA3)
- **Status:** FDA issued Complete Response Letter (CRL) — rejected without clear path forward
- **Safety:** Completely safe; improved version of riluzole (ALS drug approved 30 years ago)
- **Pending:** Waiver petition that could be approved immediately
- **Related:** 23 other rare disease drugs also received CRL rejections from FDA since 2025

---

*Last updated: February 28, 2026 — Phase 8 (Email Body Fix)*
