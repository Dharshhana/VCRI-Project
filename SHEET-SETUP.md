# Google Sheet — User Data Tracking (setup guide)

This logs every report generation to a Google Sheet so the team can see
**who generated a report, their mobile, date & time, the details they entered,
and how many times each user generated a report** — and search any user later.

**Four tabs are created automatically** the first time data arrives — a combined
**Master** tab plus one tab per species:

| Tab | What it holds |
|-----|----------------|
| **Master** | One row **per generation, all species together** — Timestamp, Date, Time, Generation #, **Species**, Name, Mobile, Email, Aadhar, Address, Bank/Branch, Breed, Unit Size, Project Cost, Loan, NPV, BCR, IRR, Avg DSCR, and the full **Details (JSON)**. This is the single place to see all activity. |
| **Cow** | One row per **Cow / Dairy** generation — every parameter the user entered for that species, in its own columns, ending with the project metrics. |
| **Goat** | One row per **Goat** generation — full goat parameters in their own columns. |
| **Chicken** | One row per **Native Chicken (Hen)** generation — full chicken parameters in their own columns. |

Each generation writes **one row to Master and one row to its species tab**.
The **Generation #** is counted per mobile across all species (from the Master tab).

---

## Step 1 — Create the Sheet
1. Go to <https://sheets.google.com> and create a **blank spreadsheet**.
2. Name it e.g. **"Dairy Report — User Log"**. (You don't need to add any columns; the script makes them.)

## Step 2 — Add the script
1. In the sheet, open **Extensions ▸ Apps Script**.
2. Delete any sample code, then **paste the entire contents of `google-apps-script.gs`**.
3. Click the **Save** icon (💾).

## Step 3 — Deploy as a Web App
1. Click **Deploy ▸ New deployment**.
2. Click the gear ⚙ next to "Select type" → choose **Web app**.
3. Set:
   - **Description:** Dairy report logger
   - **Execute as:** **Me**
   - **Who has access:** **Anyone**  *(required so the website can post to it)*
4. Click **Deploy**, then **Authorize access** and allow the permissions (it's your own script).
5. Copy the **Web app URL** — it looks like:
   `https://script.google.com/macros/s/AKfy................/exec`

## Step 4 — Connect the website
1. Open **`report.html`** in a text editor.
2. Find this line near the bottom:
   ```js
   const TRACK_URL = "";
   ```
3. Paste your Web app URL inside the quotes:
   ```js
   const TRACK_URL = "https://script.google.com/macros/s/AKfy...../exec";
   ```
4. Save. Done — every time someone generates a report, a row is added.

---

## How the team uses it
- **See all activity (any species):** open the **Master** tab — newest rows at the bottom.
  Filter the **Species** column to see only Cow, Goat or Chicken.
- **Deep-dive one species:** open its **Cow / Goat / Chicken** tab — every parameter that
  species collects is in its own column, so you can sort/compare like a spreadsheet.
- **Find a specific user:** use Google Sheets **Find** (Ctrl/Cmd + F) or a column filter on
  **Name** or **Mobile** in the Master tab. Every report that user generated is listed, with
  date, time and the figures they entered.
- **How many times a user generated:** the **Generation #** column shows the running count for
  that mobile (across all species).
- **What they entered:** the species tab holds every parameter in its own column; the Master tab
  also keeps the full **Details (JSON)** for that generation.

## Notes
- **Upgrading an existing sheet (Stage 4):** if you already deployed the older single-`Reports`-tab
  version, just replace all the Apps Script code with the new `google-apps-script.gs`, then
  **Deploy ▸ Manage deployments ▸ Edit ▸ Version: New version** (keeps the same URL — no website
  change needed). The new **Master / Cow / Goat / Chicken** tabs are created automatically on the
  next generation; any old `Reports`/`Users` tabs are left untouched (you can archive or delete them).
- **Privacy:** data is sent only to *your* Google Sheet; nothing else leaves the browser.
- **Re-deploying after edits:** if you change the `.gs` code later, use **Deploy ▸ Manage deployments ▸
  Edit ▸ Version: New version** so the URL stays the same.
- **Test quickly:** pasting the Web app URL straight into a browser runs `doGet` and should
  return `{"ok":true,...}` and add a (mostly blank) test row.
- **Counting:** a generation = one click of **Generate Report**. Refreshing the report page does
  **not** add a duplicate (it's de-duplicated per generation token).
