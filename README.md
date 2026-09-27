# Olly desktop installers

Prebuilt installers for [Olly](https://github.com/olly-ai-open/olly), an AI
chief of staff that runs entirely on your own computer.

## What Olly does

Olly connects to the tools you already work in, Google Calendar, Gmail,
Google Drive, Slack, Notion, and optionally Airtable and Jira, and turns
what it finds into a daily rhythm:

- **Morning brief.** Every morning, a summary of the day ahead: meetings,
  what came in overnight, and the open items you should not lose track of.
- **Meeting briefs.** Before each meeting, who is attending, the recent
  history with them, related documents and threads, and suggested talking
  points.
- **End-of-day summary.** What happened, what was decided, and what is
  still open, carried into tomorrow.
- **Action items.** Olly tracks the commitments it spots across your tools
  and can add them to your calendar.
- **Brief emails.** Each brief can also be emailed to you from your own
  Gmail account, a few minutes before it matters.
- **Generate Docs.** Weekly reports, status updates, or any custom document
  you describe, drafted from your own data.
- **Meeting scheduling.** Olly books meetings for you, including
  negotiating times with the other attendees over email, or booking a time
  you have already agreed.

It runs as a normal app on your Mac or PC, with its own private database.
**Olly only works while the app is running.** There is no server and no
background service: briefs, emails, and scheduling happen only while Olly
is open on your computer. Leave it running through the workday; when you
open it after a break, anything it missed catches up within a minute.
Your data and your LLM key stay on your machine; the only things that leave
it are the requests Olly makes to the AI provider you pick and to the
services you connect. Nothing is sent to the people who build Olly.

## Provided as-is

These builds are free to use and provided as-is. Because of the privacy
design above, nothing about your installation reaches us: no logs, no error
reports, no usage data, no debug information of any kind. That means we
cannot see what went wrong on your machine, and little to no support is
possible. The troubleshooting notes below cover the problems we know
about. There are no automatic updates; to update, install a newer build
over the old one and your data is kept.

## Download

The latest build is always attached to the
[desktop-latest release](https://github.com/olly-ai-open/olly-releases/releases/tag/desktop-latest):

| Your machine | Download |
|---|---|
| Mac with Apple Silicon (M1 or later) | `olly-<version>-arm64.dmg` |
| Mac with an Intel processor | `olly-<version>.dmg` (also runs on Apple Silicon through Rosetta, but the arm64 build is faster) |
| Windows 10 or 11, 64-bit | `olly.Setup.<version>.exe` |

Not sure which Mac you have? Apple menu, **About This Mac**. "Chip" means
Apple Silicon; "Processor" means Intel.

**You need to be whitelisted before you can sign in.** Google sign-in only
works for accounts that have been added as test users. Ask whoever sent you
this link to add your Google address, then continue below.

**TL;DR:** the desktop app is built with a privacy-first design. It installs
a local datastore and uses *your own LLM key*, so none of your information
ever comes to us. *Be sure to review step B to get through the security
warning.*

---

## macOS

### A. Install

1. Double-click the DMG to mount it.
2. Drag Olly into the Applications folder.
3. Eject the DMG.

### B. Get past the security warning

Olly is not notarized with Apple, so the first launch shows "Olly can't be
opened because Apple cannot check it for malicious software" (or "is
damaged" on some versions). To get past it:

1. In Applications, right-click (or Control-click) Olly, then **Open**.
2. In the dialog, click **Open** again. This whitelists the app, so future
   launches are normal.
   - On macOS Sequoia (15) and later you may instead need **System
     Settings, Privacy & Security**, scroll down, and click **Open Anyway**
     next to the Olly message.

If neither works, run this once in Terminal:

```bash
xattr -dr com.apple.quarantine /Applications/olly.app
```

### C. First launch

On first run Olly will:

- Show a "Starting Olly…" loading window.
- Set up its embedded PostgreSQL (port 54329, private to the app; no system
  Postgres is touched) and apply migrations.
- Start the bundled web server on `localhost:3000`.
- Open the setup screen, since no LLM key is bundled.

### D. Configure

1. On the setup screen, pick a provider (Claude/Anthropic, OpenAI, or
   Gemini), paste your API key, and save. The dashboard opens; no restart
   is needed.
2. Sign in with Google.
3. Open **Integrations** and connect Olly to Google Calendar, Gmail, and
   Drive, plus Notion, Slack, Airtable, and Jira if you use them.
4. Open **Settings** to check your timezone, brief times, and AI model. You
   can add a key for another provider or change models there at any time.
5. Run a morning brief from the dashboard to confirm the LLM key works end
   to end.

### E. Notes and troubleshooting

- **"Port 3000 already in use"**: quit whatever is on port 3000 and
  relaunch.
- **Stuck on "Setting up local database"**: first-run Postgres init; give
  it a moment. To see logs, run
  `/Applications/olly.app/Contents/MacOS/olly` from Terminal. Lines are
  prefixed `[olly-startup]`.
- All app data lives under `~/Library/Application Support/olly/`
  (`postgres-data/`, `state.json`, `secrets.env`). To start over, quit Olly
  and run `rm -rf ~/Library/Application\ Support/olly`.

---

## Windows

### A. Install

1. Double-click `olly.Setup.<version>.exe`.
2. The installer runs without further questions and creates a desktop
   shortcut and a Start menu entry.

### B. Get past the security warning

The installer is not code-signed, so Windows shows **"Windows protected
your PC"** with no Run button. To get past it:

1. Click **More info**.
2. Click **Run anyway**.

If the download itself is blocked by your browser or by SmartScreen, choose
**Keep** on the download prompt. Some antivirus products quarantine
unsigned installers; restore the file if that happens.

### C. First launch

On first run Olly will:

- Show a "Starting Olly…" loading window.
- Set up its embedded PostgreSQL (port 54329, private to the app; no system
  Postgres is touched) and apply migrations. Windows Firewall may ask
  whether to allow it; the database only listens on your own machine, so
  either answer is fine.
- Start the bundled web server on `localhost:3000`.
- Open the setup screen, since no LLM key is bundled.

### D. Configure

Same as on macOS, step D above.

### E. Notes and troubleshooting

- **"Port 3000 already in use"**: quit whatever is on port 3000 and
  relaunch.
- **Stuck on "Setting up local database"**: first-run Postgres init; give
  it a moment. Log lines are prefixed `[olly-startup]`.
- All app data lives under `%APPDATA%\olly\` (`postgres-data\`,
  `state.json`, `secrets.env`). To start over, uninstall Olly from
  Windows Settings, which removes that folder too, then reinstall.

---

## F. Using Olly regularly

Once your tools are connected, the briefs described at the top of this
page generate on their own schedule, which you set in **Settings**. Two
things to know:

- Nothing runs while the app is closed or your computer is asleep. Keep
  Olly open during the day, and do not quit it if you expect a meeting
  brief or an emailed brief. When you open it again, pending briefs catch
  up within a minute.
- Olly re-reads your calendar on an interval (hourly by default, adjustable
  in Settings). A meeting added or moved less than an interval before it
  starts may not get an automatic brief; click **Generate brief** on its
  card in the dashboard instead.

*Note: the setup requires you to provide your own LLM key. Your data and
your key stay on your machine.*

## Privacy

Everything Olly stores lives in the app data folder on your computer: the
database, the encryption keys it generates for itself on first launch, and
your LLM key. The only data that leaves your machine goes to the LLM
provider you chose and to the services you connect. Nothing is sent to the
people who build Olly, which is also why we cannot see your logs or help
with problems on your machine.

Source code, the security model, and build instructions are in the
[Olly repository](https://github.com/olly-ai-open/olly).
