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
Events carry a 5-minute alert, and they move each time you log a feed or change.
(Share the calendar with your partner from Google Calendar → Settings → Share.)

## Tabs
- **Track**: log a feed by picking any mix of breast, formula and pumped milk, each with its own amount (mL/oz, in 5 mL steps) — breast also gets minutes and a side. Tap "＋ Spit-up or note", and see a "Same as last" one-tap button once you've logged one.
- **Nursing timer**: tap **▶ Start nursing timer** on the Feeding card when she latches, **⇄ Switch side** if she swaps, **■ Stop & log feeding** when done — it fills in the duration and the exact start time for you, so there's nothing to estimate or type.
- **Pump / Feed**: next feed and next pump side by side with a merged "coming up" list, pumping sessions with duration and total (optionally left/right), 24-hour totals, and a 7-day **milk supply vs. what she needs** chart — daily production (pumped mL, plus nursing sessions assumed at 15 mL each, since actual transfer isn't measurable) against her typical daily need, with the formula gap spelled out for today.
- **Journal**: one-tap "how is she doing", notes and tags, a dedicated **⚖️ Log weight** button, a weight-trend chart, and length, head size and temperature (US or metric).
- Tap any entry (or its ✎) to edit its time, details and unit.
- Volume units default to mL for the first 2 weeks, then oz. Change that in ⚙ Settings.

There's no diaper tracking. In the Sheet these are the `Log`, `Pumping` and `Journal`
tabs. Amounts are always stored in mL (weights in g, lengths in cm, temperatures in
°C); each row also records the unit it was entered in.

### Feeding and pumping stay in sync
The Pump / Feed tab exists to track milk production and frequency, not just sessions
on an actual pump — so:
- **Breastfeeding with a logged duration also creates a matching entry on Pump / Feed**
  (marked "fed" at that same moment). Change the duration later and that entry updates;
  clear it and the entry is removed. This is what the nursing timer feeds into.
- **A pumping session has its own independent "Fed to baby?" yes/no and time**, separate
  from when it was pumped. Log the session, store it, and come back later — even the
  next day — to mark it fed at the real time; that creates (or updates) a matching feed
  entry on Track. Unmarking it removes that feed entry again.
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
- `…/#quick=feed-repeat` — logs "Same as last", identical to tapping that button

**To wire up "Hey Siri, start nursing":**
1. Open the **Shortcuts** app on iPhone → **+** to create a new shortcut.
2. Add the action **Open URLs**, and set the URL to your site plus the hash, e.g.
   `https://<you>.github.io/<repo>/#quick=timer-start`.
3. Tap the shortcut's settings (⋯) → **Add to Siri**, and record a phrase like "start nursing."
4. Repeat for "stop nursing" (`#quick=timer-stop`) and "log a feed" (`#quick=feed-repeat`).

This briefly opens Safari to run the action and shows a confirmation there — it isn't
fully invisible, but it's two spoken words instead of unlocking the phone and filling in
a form. It only works on a device that's already connected (step 3 above), since the
timer and log both need to know whose data they're touching.

## Changing the backend
After editing `Code.gs`: **Deploy → Manage deployments → ✎ → Version: New version → Deploy**. The URL stays the same.
To rotate the secret, change `SECRET`, redeploy, then re-open the setup link with the new key.

## Without the Sheet
`index.html` also works standalone: data stays in that one browser, and the **Download .ics**
button gives you a calendar file (re-importing it updates the same events).
