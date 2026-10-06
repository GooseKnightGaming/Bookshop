# Shop Stock

A phone-first stock tracker for a secondhand bookshop. It scans barcodes, reads publisher pages, counts books in and out, and remembers everything you've ever had.

It runs entirely in your phone's browser. There's no server, no account and no cost.

## Put it on GitHub Pages

1. Create a new repository on GitHub, for example `shop-stock`. It can be public or private (private Pages needs a paid plan).
2. Upload everything in this folder: `index.html`, `manifest.webmanifest`, `icon-180.png`, `icon-512.png` and this README. `Code.gs` can go in too for safekeeping; it isn't used by the website itself. On GitHub that's **Add file → Upload files**.
3. Go to **Settings → Pages**. Under *Build and deployment*, choose **Deploy from a branch**, pick `main` and `/ (root)`, then **Save**.
4. After a minute or two the site appears at `https://<your-username>.github.io/shop-stock/`.
5. Open that address on your phone, then add it to your home screen:
   - **iPhone (Safari):** Share → Add to Home Screen.
   - **Android (Chrome):** ⋮ menu → Add to Home screen / Install app.

Opening it from the home-screen icon is worth doing. It runs full-screen, and on iPhone it stops Safari clearing the saved data when you haven't visited for a while.

## How it finds book details

- **Barcodes** are scanned live with the camera (ZXing). This needs the browser to be allowed to use the camera; if it isn't, use "Photo or type" instead.
- **ISBNs** are looked up on Open Library, then Google Books. Both are free and need no key.
- **Publisher pages and covers:** take the photo with your normal camera app, then pick it from your gallery. It's read on the phone itself with Tesseract (text recognition). The first time, it downloads about 10 MB of language data, which the browser then keeps. It pulls out any ISBN (including old 9-digit SBNs), the publisher, the "first published" year and the edition. With no ISBN, it searches Open Library using the words it read and lets you pick the match.
- **ISBNs can be typed** into the "ISBN or barcode" box, then tap Look up. If the number has a typo, it says so (the last digit is a check digit) and searches anyway.
- **Genre and section** are first guessed from the subjects the book databases return. That's only ever rough. They don't know, for example, whether an author is a woman, so a woman's crime novel comes back as plain fiction.
- **The app learns from you.** Whenever you save an item with a section, it remembers that author's section and genre. The next book by them fills in automatically, and the form says so. If an author writes across sections, change it on the form and the newer choice becomes the default.
- **Same book, different ISBN:** if a new ISBN matches a title and author you already have, it offers "Count it as another copy of that". It remembers the extra ISBN, so next time either one finds the same record.

## Share between phones (Google Sheet)

The app can keep several phones in step through a Google Sheet you own. The other volunteer doesn't need a Google account, and the sheet doubles as a stock spreadsheet anyone you share it with can read.

**One-off setup (about 10 minutes, on a computer is easiest):**

1. Create a new Google Sheet, called something like *Shop Stock*.
2. In the sheet, go to **Extensions → Apps Script**. Delete what's there and paste in the whole of `Code.gs`.
3. On the line `const SECRET = "change-this-to-your-shop-code";`, change the text in quotes to your own shop code: any word or phrase. Save (the disk icon).
4. Click **Deploy → New deployment**. Click the cog next to *Select type* and choose **Web app**. Set *Execute as* to **Me** and *Who has access* to **Anyone**. Click **Deploy**.
5. Google asks you to authorise it. Choose your account, then **Advanced → Go to … (unsafe)** → **Allow**. This warning appears because it's your own unpublished script; it only gets access to this sheet.
6. Copy the **Web app URL** it gives you.

**Connecting the phones:**

1. On your phone, open the app, tap **Sharing**, paste the web app URL, type your shop code, and tap **Connect**. Anything you've already scanned goes up to the sheet.
2. Tap **Copy setup link for another phone** and send it to the other volunteer. It contains the shop code, so only send it to people you trust.
3. On their phone, they open the app (ideally from the home-screen icon), tap **Sharing**, paste the setup link, and tap **Connect**. Opening the setup link directly in their browser works too. On iPhone, though, the home-screen app keeps its own separate storage from Safari, so pasting the link into Sharing inside the home-screen app is the reliable way.

**How syncing behaves:**

- Each phone checks the sheet every 45 seconds while the app is open, and straight after any change.
- Phones send *actions* ("one in", "one out"), not whole records, so two people scanning the same book at the same time both count correctly.
- With no signal, changes wait on the phone and send once it's back online. The status line under the totals shows if anything is waiting.
- Deleted records stay in the sheet with a 1 in the *deleted* column, so other phones know to remove them.

**Your categories live in the sheet.** Besides *Stock*, the script makes two more tabs:

- **Sections**: one row per section, with four columns:
  - *Group* is the umbrella (Adult Fiction, Kids' Non-Fiction…).
  - *Section* is the shelf. For deeper levels, write them with `>`, as in `History > 20th Century > WW2`; it can go as deep as you need. On the phone these show indented under their parent (when the parent has its own row).
  - *Sub-sections* is optional: comma-separated suggestions offered on the phone, such as cuisines, sports, languages, or counties under `British Isles > England`. People can still type their own.
  - *Where it's kept* is optional, for anything not on the shop floor, such as `Storage room (upstairs)` or `Mills & Boon box`. The phone shows it in bold on the item, so whoever looks it up knows where to go.

  A book's section is stored written out in full, like `Adult Non-Fiction > History > Roman Britain`. Add, rename, reorder or delete rows when the shop gets rearranged, and the phones follow within a minute. Items in a removed section show "(old section)" until you change them.
- **Authors**: which section, sub-section and genre each author's books normally go in. The app fills this in as you work, and you can correct or add rows by hand.

You can also edit the *Stock* tab directly. Fix a section, genre or title in a cell and the phones pick it up. Don't edit the `count`, `id`, `updated` or `log` columns; let the app handle those. You can add your own extra columns too, and they'll be left alone.

**Getting a newer built-in section list:** when the app is updated with new sections, use **Shop Stock → Load the updated section list (keeps a backup)**. Your current tab is renamed *Sections backup (date)*, the new list goes in, and books in sections that were renamed move across automatically. Anything you'd added yourself is still in the backup tab, ready to copy back in.

**After a big find-and-replace** (say you rename a section and update every book in it), use the **Shop Stock** menu at the top of the sheet → **Send sheet changes to the phones**. Find-and-replace doesn't always count as an edit, so this makes sure every phone catches up. The menu appears after you reload the sheet once.

If your sheet was set up with the first version's two-letter section codes, the new script renames that tab to *Old sections (codes)*, creates the new *Sections* tab, and moves existing books and author rules across to the full names automatically. You can delete the old tab afterwards.

**If you change `Code.gs` later:** go to **Deploy → Manage deployments**, click the pencil, choose **New version**, then **Deploy**. This keeps the same web app URL. A brand-new deployment would give a new URL, which every phone would need.

## Your data

- Everything is stored on the phone, in that browser, using IndexedDB. With Sharing switched on, it's also kept in your Google Sheet.
- **Back up** downloads a `.json` file with everything, history included. **Restore or import** loads it back in, on the same phone or a new one.
- **Download spreadsheet** gives a CSV for Excel or Google Sheets. Restore also accepts a CSV with the same column names, so data exported from the earlier Claude version can come across.
- The app nags you once there are 10+ items and you haven't backed up for two weeks.

## Updating

Edit `index.html` (on GitHub you can click the file, then the pencil icon), commit, and the site updates within a couple of minutes. Your data on the phone isn't affected.
