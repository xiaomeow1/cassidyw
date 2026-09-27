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
- **Track**: log a feed with just a source (breast or bottle) and an amount in mL or oz, tap "＋ Spit-up or note", and see a "Same as last" one-tap button once you've logged one.
- **Pump / Feed**: next feed and next pump side by side with a merged "coming up" list, plus pumping sessions with duration, total (optionally left/right), where it went, 24-hour totals, a 7-day chart, and pumped-and-stored milk on hand.
- **Journal**: one-tap "how is she doing", notes and tags, a dedicated **⚖️ Log weight** button, a weight-trend chart, and length, head size and temperature (US or metric).
- Tap any entry (or its ✎) to edit its time, details and unit.
- Volume units default to mL for the first 2 weeks, then oz. Change that in ⚙ Settings.
- **Breastfeeding with an amount also logs a pumping-tab entry** (as milk delivered straight from the source), so total intake stays in one place.
- A pumping session has its own independent **"Fed to baby?"** yes/no and time, separate from when it was pumped. Log the session, store it, and come back later — even editing it the next day — to mark it fed at the actual time; that creates (or updates) a matching feed entry on Track. Unmarking it removes that feed entry again.

There's no diaper tracking. In the Sheet these are the `Log`, `Pumping` and `Journal`
tabs. Amounts are always stored in mL (weights in g, lengths in cm, temperatures in
°C); each row also records the unit it was entered in.

## Using it
- **Log feeding**, back-date with the *When* field. Entries show instantly and sync in the background. If you're offline they queue and send later.
- Anyone using the link sees the same data (the page checks for updates about every 45 s and when reopened).
- In the Sheet, **Log** is the full feed/note history, **Pumping** and **Journal** are their own tabs, **Daily** has per-day totals (handy for pediatrician visits), and **Settings** holds name, birth time and feed spacing (you can edit them there too).

## Changing the backend
After editing `Code.gs`: **Deploy → Manage deployments → ✎ → Version: New version → Deploy**. The URL stays the same.
To rotate the secret, change `SECRET`, redeploy, then re-open the setup link with the new key.

## Without the Sheet
`index.html` also works standalone: data stays in that one browser, and the **Download .ics**
button gives you a calendar file (re-importing it updates the same events).
