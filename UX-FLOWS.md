# UX Flows

Page-by-page user experience for Know-Thyself v0.1.
Bridges the product spec (README) with the architecture (ARCHITECTURE.md).
Draws on research insights from `research/gpt-research-4-2-26.md`.

---

## 1. Today's Prompt (`/N` where N = today)

**What the user sees:**

- Hebrew prompt text, large and centered, RTL
- Below: text area with one-time placeholder: *"זה רק בינך לבין עצמך. כתוב בכנות."*
- Typing starts immediately, no login required
- Subtle draft indicator appears on first keystroke: **"נשמר מקומית"** (Saved locally)
- Draft autosaves to `localStorage` on every keystroke

**Finalize flow:**

1. User taps **"סיימתי"** (I'm done)
2. If logged in: answer saves server-side, button becomes a checkmark, text area locks
3. If not logged in: lightweight login prompt appears *inline* (not a redirect). Message: **"כדי לשמור, צריך להתחבר."** (To save, you need to log in.) Google button below.
4. After login, finalize completes automatically with the draft content
5. No editing after finalize. One-way door.

**Why this order:** Starting writing before auth follows the Hook model (trigger > action > investment). The user's own words become the motivation to log in. Research (Fogg's B=MAP) confirms reducing upfront friction increases completion.

---

## 2. Past Prompt (`/N` where N < today)

**What the user sees:**

- The prompt text (read-only, same styling)
- If the user finalized this day: their answer below, locked, with a small checkmark
- If not finalized: **no text area**. A quiet one-liner: **"השאלה הזו עברה. היום יש חדשה."** (This question has passed. Today has a new one.)
- Link/arrow pointing to today's prompt

**Design intent:** Non-punishing miss state. No guilt, no empty text box taunting them. Research on streak culture warns that shame-driven miss UX causes dropout. The tone says "today is new" rather than "you failed."

---

## 3. Future Prompt (`/N` where N > today)

- 404 page
- Message: **"עוד לא הגענו לשם."** (We haven't gotten there yet.)
- Link to today's prompt

---

## 4. Shabbat Rest (Saturday)

- No writing task
- The page shows a quiet mini-quote or single calming sentence
- Example: **"היום לא כותבים. פשוט להיות."** (Today we don't write. Just be.)
- No text area, no finalize button
- Previous/next navigation still works for browsing past prompts
- This day does not count toward streaks and does not break them

**Implementation:** Saturday detection based on Israel timezone (IST/IDT). The prompt sequence skips Saturdays: prompt #1 = day 1 (not Saturday), prompt #2 = day 2 (not Saturday), etc.

---

## 5. "Nothing Comes to Mind" Fallback

- Small link below the text area: **"לא בא לך? כתוב מה שיש."** (Not feeling it? Write what's there.)
- Tapping it replaces the prompt with the free-reflection instruction: **"אם השאלה לא מדברת אליך היום, עצור רגע וכתוב מה שנוכח עכשיו. בלי מבנה. בלי ביצועים. רק הרגע."**
- Free reflection counts fully as presence when finalized
- Tracked internally with a flag (`is_free_reflection: true`) for quarterly review analytics

---

## 6. Weekly Reflection (Friday)

**Trigger:** Friday in Israel timezone. Appears as a separate section below the daily prompt.

**What the user sees (after answering or scrolling down):**

- Section header: **"סיכום שבועי"** (Weekly Summary)
- Read-only cards showing the user's finalized answers from the past 6 days (Sunday-Friday)
- Days without finalized answers are omitted (no empty slots)
- Below the review cards: a text area for the weekly summary
- Placeholder: **"מה עולה כשאתה מסתכל על השבוע?"** (What comes up when you look at the week?)
- **"סיימתי"** button for the summary (same flow as daily finalize)

**Privacy:**

- Weekly summary is private by default
- After finalize, a toggle appears: **"לשתף את הסיכום?"** (Share this summary?)
- Sharing opt-in is per-summary, never a global setting
- Shared summaries only; daily answers are never shareable

**v0.1 scope note:** Weekly reflection may be deferred past initial launch. The architecture supports it (same `answers` table with a `type` flag), but Ron should confirm priority.

---

## 7. Private Dashboard (`/me`)

- **Total Days Submitted:** number
- **Current Streak:** number with a small flame or dot indicator
- **Calendar heatmap:** simple grid showing finalized days (filled) vs missed days (empty), current month
- **List of finalized prompts:** date + first few words of the prompt, tappable to view

**Streak display:** Always private, never public. Streak resets are shown neutrally (no "you lost your streak!" messaging). The counter simply shows the current number.

**v0.1 scope note:** Calendar heatmap is nice-to-have. The minimum is the two counters and a list.

---

## 8. Homepage (`/`, not logged in)

- Three-line message (centered, large):
  > **"משחק כתיבה יומי ופרטי."**
  > **"בלי נקודות. בלי מנצחים."**
  > **"להגיע. לכתוב בכנות. לשים לב למה שיש."**
- Below: **"להתחיל"** (Start) button that redirects to today's prompt
- Small footer link: Privacy policy

**No login required to start.** The homepage exists for first-time visitors only. Returning users go straight to `/` which redirects to today's prompt.

---

## 9. Share Preview (OG Tags)

When a prompt URL is shared on social media:

- **og:title:** The prompt text (Hebrew)
- **og:description:** "משחק כתיבה יומי ופרטי" (A private daily writing game)
- **og:image:** Unique prompt image (generated per prompt, see below)
- **og:url:** The prompt page URL

**Prompt images:** Each prompt gets a unique generated image that captures the essence of the question. Sized 1200x630 for optimal social preview. Abstract/evocative style, not literal. Hebrew text of the prompt overlaid on the image.

**v0.1 scope note:** Prompt images can launch as a simple branded card (prompt text on a designed background) without AI generation. AI-generated unique art per prompt is a post-v0.1 enhancement.

---

## 10. Global Presence Indicator (post-v0.1)

- On each prompt page: **"X אנשים הגיעו היום."** (X people showed up today.)
- Small, non-prominent placement (footer or subtle top bar)
- Optional 30-day sparkline of daily submissions
- No usernames, no ranking, no comparison
- Deferred past v0.1 (requires meaningful user base to be non-trivial)

---

## 11. Login/Auth

- Google only for v0.1
- Login appears only when needed (finalize without session)
- Inline prompt, not a separate page redirect
- After login, user returns to exact same prompt with their draft intact
- Session persists via cookie/token

---

## 12. Mobile and RTL

- All pages are mobile-first
- RTL throughout (`dir="rtl"`, `lang="he"`)
- Text area: full width, generous height, large readable font
- Finalize button: prominent, centered below text area
- Touch targets: minimum 44x44px

---

## Edge Cases

| Scenario | Behavior |
|----------|----------|
| User visits `/` at 11:59 PM, finishes writing at 12:01 AM | Draft saves for yesterday's prompt. If they finalize, it counts for yesterday. The date is locked when the page loads. |
| User clears browser data | Local draft is lost. Message: "No draft found." No guilt. |
| User visits on two devices | Each device has its own local draft. First finalize wins. Second device shows the finalized answer on next visit. |
| Network error on finalize | Retry button. Draft stays in localStorage until successful save. |
| User tries to finalize empty text | Button is disabled until at least 1 character is entered. |

---

## Research-Backed Decisions Summary

These UX choices incorporate findings from `research/gpt-research-4-2-26.md`:

1. **Write before login** (Fogg B=MAP: reduce friction, increase ability)
2. **Non-punishing missed days** (loss aversion research: avoid shame-driven dropout)
3. **"Saved locally" indicator** (privacy posture: just-in-time disclosure, not legal dump)
4. **Saturday rest** (cultural alignment + prevents streak pressure on Shabbat)
5. **Free reflection fallback** (avoids forced disclosure; gratitude/observation as safe defaults)
6. **Share question, never answer** (enables growth without self-exposure)
7. **Private streaks only** (Duolingo research: commitment without public pressure)
8. **OG image spec** (Meta requirements: 1200x630, Open Graph protocol)
