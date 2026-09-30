# Newborn tracker

Feeding, pumping and weight log with next-time estimates, a newborn guide, and an
auto-updating calendar. Runs from anywhere: the page is hosted on GitHub Pages, the
data lives in **your Google Sheet**, and the schedule is written to a **Google
Calendar** that your iPhone shows natively.

```
iPhone / any browser ──► GitHub Pages (index.html, no personal data)
        │
        └─► Google Apps Script (apps-script/Code.gs, protected by your secret)
                 ├─► Google Sheet   Log · Pumping · Journal · Settings · Daily
                 └─► Google Calendar  next 4 feeds + next 3 pumps, moved on every log
```

The repo holds no name, birth date, or secret. Those live in your Sheet and in the
browser you use.

## One-time setup (about 10 minutes)

### 1. The Sheet and its backend
1. Create a new Google Sheet at <https://sheets.new> (name it e.g. "Baby tracker").
2. **Extensions → Apps Script.** Replace everything in `Code.gs` with `apps-script/Code.gs` from this repo.
3. Change `SECRET` at the top to a long random string. One way to make one:
   ```
   openssl rand -hex 16
   ```
4. Pick **`setup`** in the function dropdown and press **Run**. Approve the permissions
   (Google will say the app is unverified because it's your own script: Advanced → Go to project).
   This creates the `Log`, `Pumping`, `Journal`, `Settings`, and `Daily` tabs and a calendar named after the baby.
5. **Deploy → New deployment → ⚙ Web app**. Execute as **Me**, who has access **Anyone**. Deploy and copy the **Web app URL**.
   ("Anyone" only means the URL is reachable. Every request must carry your secret, or it is refused.)

### 2. Host the page on GitHub
1. Create a repository (public is fine, it contains no personal data; free Pages needs public).
2. Upload `index.html` (plus `apps-script/` and `README.md` if you like): **Add file → Upload files**.
3. **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)`**.
   Your site appears at `https://<you>.github.io/<repo>/`.

### 3. Connect each device once
Open this link on the phone or computer (it stays in that browser; keep it private):

```
https://<you>.github.io/<repo>/#u=<WEB APP URL>&k=<SECRET>
```

The page saves the connection, clears the link from the address bar, and asks for the
baby's name and birth date/time (saved to your Sheet's `Settings` tab).
On iPhone: Share → **Add to Home Screen**.

### 4. Get the calendar on the iPhone
**Settings → Calendar → Accounts → Add Account → Google**, sign in with the same Google
account and turn on **Calendars**. The baby's calendar appears in the Calendar app.
If it doesn't, open <https://calendar.google.com/calendar/syncselect> and tick it.
Events carry a 10-minute alert, except predicted overnight feeds (midnight–6 AM),
which sync to the calendar without an alert so they don't wake you before she does.
They move each time you log a feed or change. (Share the calendar with your partner
from Google Calendar → Settings → Share.)

## Tabs
- **Feed & Pump**: feeding and pumping side by side (two columns on a wide screen,
  stacked but clearly separate on a phone) — they're never blended into one card,
  since pumping milk doesn't mean it's been fed yet.
  - **Feeding**: the fields are right there on the card, not behind a button — pick any mix of breast, formula and pumped milk, each with its own amount (mL/oz, in 5 mL steps) and breast's minutes and side, then **🍼 Log feeding**. Advice inline (typical amount, timing) adjusts to her day of life. A "Same as last" one-tap button sits above it once you've logged one; tap "＋ Spit-up or note" for that. A feeding timeline and the upcoming-feeds/calendar controls sit below.
  - **Timers**: one shared **Timers** card, right below the next-feed/next-pump summary, holds both — **▶ Start nursing timer** when she latches, **⇄ Switch side** if she swaps, **■ Stop & log feeding** when done; **▶ Start pumping timer** the same way, then **■ Stop & log pumping** fills in the duration and opens a form so you can add how much came out. Both can run at once, and each fills in the exact duration and start time for you, so there's nothing to estimate or type. The matching card's inline fields hide themselves while its timer is running.
  - **Type or say it**: below Timers, a single box for when tapping through chips is more than you want — type (or dictate) a sentence like *"pumped 90ml for 15 min"* or *"breastfed 12 min left side"* and tap **Log it**. It looks for what happened ("pumped", "breastfed"/"nursed", "formula"), an amount (mL/oz) or duration (minutes), "ago" phrasing, a side, and whether it was fed to her — same Undo toast as everything else if it gets something wrong. On iPhone, tap the microphone on the keyboard to dictate into the box; desktop/Android Chrome also get a 🎤 button on the box itself for tap-to-talk. It only trusts numbers that carry a unit ("90ml", "15 min"), so it'll ask you to be specific rather than guess from a bare number.
  - **Pumping**: the duration/amount/left-right/fed-to-baby fields are right on the card too — fill in and **🥛 Log pumping session**, no button to open first. Below that: 24-hour totals, a 7-day **breast milk & formula vs. what she needs** chart (what she actually took in each day, split by source, against her typical daily need — logging a pump session alone never counts here, only what's marked fed), and a 7-day **daily pumping output** chart (just what came out of the pump, day by day — pure supply, unrelated to feeding).
  - **Recent activity**: feeds, pumping sessions and notes combined into one chronological list (no more separate, toggled Feed log / Pumping sessions), showing the last 10. The sleep-safety guide sits just above it.
- **Journal**: one-tap "how is she doing", notes and tags, a dedicated **⚖️ Log weight** button, a weight-trend chart, and length, head size and temperature (US or metric).
- Tap any entry (or its ✎) to edit its time, details and unit — that still opens a form of its own, separate from the inline logger.
- Volume units default to mL for the first 2 weeks, then oz. Change that in ⚙ Settings.

There's no diaper tracking. In the Sheet these are the `Log`, `Pumping` and `Journal`
tabs. Amounts are always stored in mL (weights in g, lengths in cm, temperatures in
°C); each row also records the unit it was entered in.

### Feeding and pumping stay in sync — without being the same thing
Pumping milk isn't the same event as feeding it to her, so they're tracked separately
and only linked when that's actually true:
- **Breastfeeding with a logged duration shifts "next pump" timing on Pumping**, so the
  prediction and calendar reminder account for it — but it's timing-only. It never shows up
  as a session in the Pumping card, tiles, Recent activity or the daily-output chart, and it never
  adds any mL: pumping and feeding stay separate events, only the schedule reacts to both.
  Change the duration later and the timing updates; clear it and it stops counting. This is
  what the nursing timer feeds into.
- **A pumping session has its own independent "Fed to baby?" yes/no and time**, separate
  from when it was pumped, and it defaults to "Not yet" — logging a pump session never
  counts as a feed on its own. Log the session, store it, and come back later — even the
  next day — to mark it fed at the real time; that creates (or updates) a matching feed
  entry on Feeding. Unmarking it removes that feed entry again.
- Either side can be deleted from its own tab, and its mirrored entry goes with it.

## Using it
- **Log feeding**, back-date with the *When* field. Entries show instantly and sync in the background. If you're offline they queue and send later.
- Anyone using the link sees the same data (the page checks for updates about every 45 s and when reopened).
- In the Sheet, **Log** is the full feed/note history, **Pumping** and **Journal** are their own tabs, **Daily** has per-day totals (handy for pediatrician visits), and **Settings** holds name, birth time and feed spacing (you can edit them there too).

## Hands-free: log with Siri
The page understands a few `#quick=` links that act the instant they load — no taps.
Opening one of these (in Safari, or via a Shortcut) does it immediately:

- `…/#quick=timer-start` — starts the nursing timer
- `…/#quick=timer-stop` — stops it and logs the feed right away (with an Undo toast)
- `…/#quick=pump-timer-start` — starts the pumping timer
- `…/#quick=pump-timer-stop` — stops it and logs the session right away (with an Undo toast)
- `…/#quick=feed-repeat` — logs "Same as last", identical to tapping that button
- `…/#quick=feed-custom&src=…&ago=…&min=…&amt=…` — logs a feed with values you supply, for
  when it's *not* the same as last. `src` is `breast`, `formula` or `pumped` (matched loosely,
  so dictated text like "pumped milk" still works, and anything unclear defaults to formula —
  there's always an Undo toast if Siri mishears you); `ago` is minutes before now it started;
  `min` is nursing minutes (for breast); `amt` is the amount in mL (for formula/pumped).

**To wire up "Hey Siri, start nursing" (or stop-nursing / start-pumping / stop-pumping / same-as-last):**
1. Open the **Shortcuts** app on iPhone → **+** to create a new shortcut.
2. Add the action **Open URLs**, and set the URL to your site plus the hash, e.g.
   `https://<you>.github.io/<repo>/#quick=timer-start`.
3. Tap the shortcut's settings (⋯) → **Add to Siri**, and record a phrase like "start nursing."
4. Repeat for the others: "stop nursing" (`#quick=timer-stop`), "start pumping"
   (`#quick=pump-timer-start`), "stop pumping" (`#quick=pump-timer-stop`), "log a feed"
   (`#quick=feed-repeat`).

**To wire up "Hey Siri, log a feed" that asks what actually happened**, instead of always
repeating the last one:
1. Create a new shortcut, and add **Ask for Input** (Text) — prompt: "What did she have?"
   — save the answer as a variable, e.g. `What`.
2. Add **Ask for Input** (Number) — "How many minutes ago did it start?" → save as `Ago`.
3. Add **Ask for Input** (Number) — "Nursing minutes, or zero" → save as `Min`.
4. Add **Ask for Input** (Number) — "Amount in milliliters, or zero" → save as `Amt`.
5. Add **Text**, and build (tap to insert each variable from the blue "+" picker):
   `https://<you>.github.io/<repo>/#quick=feed-custom&src=What&ago=Ago&min=Min&amt=Amt`
6. Add **Open URLs**, set to the output of that Text action.
7. Name it, **Add to Siri** with a phrase like "log a feed."

Siri reads each question aloud and listens for your answer, so the whole thing happens without
looking at the phone — it just takes four short back-and-forths instead of one word, since Shortcuts
can't freely parse a single sentence the way a person would. Say "zero" for whichever of
duration/amount doesn't apply (e.g. amount for a breastfeed).

These briefly open Safari to run the action and show a confirmation there — it isn't fully
invisible, but it's a few spoken words instead of unlocking the phone and filling in a form.
They only work on a device that's already connected (step 3 above), since the timer and log
both need to know whose data they're touching.

## Changing the backend
After editing `Code.gs`: **Deploy → Manage deployments → ✎ → Version: New version → Deploy**. The URL stays the same.
To rotate the secret, change `SECRET`, redeploy, then re-open the setup link with the new key.

## Without the Sheet
`index.html` also works standalone: data stays in that one browser, and the **Download .ics**
button gives you a calendar file (re-importing it updates the same events).
