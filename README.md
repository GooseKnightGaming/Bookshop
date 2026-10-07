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

## Using it

The current version is shown at the bottom of the app (for example *Alpha 1.0.15*). The last number goes up with each update.

- **Scan** reads a book's barcode with the live camera. If the browser isn't allowed to use the camera, it offers **Take a photo** (opens the camera app) or **Choose from your photos** instead, and remembers that choice.
- **Add item** starts with the **type**: Book, Jigsaw, Game, Puzzle book, Magazine, Map or Other. The form then only asks what matters for that type. Books get ISBN, photo lookup, title and author. Jigsaws get maker and pieces, and games get maker (neither is checked for missing pieces, per the shop's disclaimer). Magazines get name and issue. Maps get series and number. Each type also starts in its usual section. Publisher, year, notes and (for non-books) barcode are tucked under **More details**.
- **Section** is chosen one level at a time: the group first, then the section, then any deeper level. Each list only shows what belongs under the one above. If the section has sub-section suggestions (cuisines, counties, languages, jigsaw sizes), a sub-section list appears, with **Other…** for anything else. Where something is kept off the shop floor, the form says so.
- **The stock list** stays searchable while you scroll. Filter it by section, sort by author, title or newest, and long lists are split by letter. Each row shows the author (surname first), the section, and where it's kept if it's not on the shop floor. The *Gone* tab shows the most recently gone first.
- **QR code for another phone** (at the bottom, and in Sharing) shows a code to scan with another phone's camera. Once you're connected to the shared sheet, it's a setup code: the other phone opens the app and connects in one go.

## How it finds book details

- **ISBNs** are looked up on Open Library and Google Books together. Open Library's record for that exact edition comes first, and Google fills any gaps (publisher, year, language). Google Books sometimes returns a different book for an ISBN search, so its result is only used if it actually carries that ISBN. If the two disagree about the author, the form says so and offers the other one. **Not right? See other matches** asks every source (including the libraries below) and lets you pick.
- If neither has it: Open Library's search index, then five free library catalogues at once: the Library of Congress, the German National Library, the French National Library, the Polish National Library and Crossref (academic books and textbooks). The book's own country is asked first (978-3 German, 978-2 French, 978-83 Polish). None need a key. Phones often aren't allowed to ask the libraries directly, so your Google Sheet's script asks for them; it can only ever fetch from those five. The British Library has no public lookup at the moment, and WorldCat needs a paid licence.
- **Searching by title and author** finds the *work*, not your copy, so the publisher and year are left blank rather than borrowed from some other edition. The ISBN is never borrowed from a search result either; it only comes from your scan or your typing.
- **Barcode photos:** the photo is searched for a barcode at full resolution, in close-up sections, and turned sideways, so a small barcode on a whole-cover photo still reads. Most Android phones also have a built-in barcode reader, which the app uses first. Only book-type barcodes are read. If one reads as something other than an ISBN, the form shows the number and why (a magazine ISSN starting 977, an American shop UPC, or a misread).
- **Cover and publisher-page photos** (no barcode found): the words are read, any ISBN on the page is looked up, and the publisher, "first published" year and edition are taken from the page. With no ISBN, the words are searched on Open Library and Google Books and you pick the match. The words are read by **Google's text reader** through your sheet's script, which is far more accurate than the phone's own reader (see "Turn on Google's text reader" below). Without it, the phone's own reader (Tesseract) is used, which struggles with real photos.
- **ISBNs can be typed** into the ISBN box, then tap Look up. If the number has a typo, it says so (the last digit is a check digit).
- **Books in another language** (when the database says so) go to *Foreign Languages*, with the language as the sub-section, even if the author's usual section is elsewhere. The app never learns Foreign Languages as an author's usual section.
- **Sections** are first guessed from the subjects the book databases return. That's only ever rough. They don't know, for example, whether an author is a woman, so a woman's crime novel comes back as plain fiction. (Genre is no longer shown; the section does that job.)
- **The app learns from you.** The first time you save a book by a new author, it remembers their section, and the next book by them fills in automatically. If you later save one of their books somewhere else, the form offers a tick-box, *Make this the usual section for…*: leave it unticked for a one-off, tick it to change their usual section.
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

**Your categories live in the sheet.** Besides *Stock*, the script makes two more tabs:

- **Sections**: one row per section, with four columns:
  - *Group* is the umbrella (Adult Fiction, Kids' Non-Fiction…).
  - *Section* is the shelf. For deeper levels, write them with `>`, as in `History > 20th Century > WW2`; it can go as deep as you need. On the phone these show indented under their parent (when the parent has its own row).
  - *Sub-sections* is optional: comma-separated suggestions offered on the phone, such as cuisines, sports, languages, or counties under `British Isles > England`. People can still type their own.
  - *Where it's kept* is optional, for anything not on the shop floor, such as `Storage room (upstairs)` or `Mills & Boon box`. The phone shows it in bold on the item, so whoever looks it up knows where to go.

  A book's section is stored written out in full, like `Adult Non-Fiction > History > Roman Britain`. Add, rename, reorder or delete rows when the shop gets rearranged, and the phones follow within a minute. Items in a removed section show "(old section)" until you change them.
- **Authors**: which section, sub-section and genre each author's books normally go in. The app adds new authors as you work (written surname-first), and you can correct or add rows by hand.
  - Names match either way round (`Rankin, Ian` = `Ian Rankin`), and pairs in either order. Separate people with `&`; a comma on its own means surname-first.
  - Accents, ø/æ, capitals, full stops and apostrophes are ignored, so `Carré` = `Carre` and `O'Brian` = `OBrian`. Words in brackets, like `(physicist)`, are labels for you only.
  - *Clues* (optional): an author can have several rows. Give each extra row a few comma-separated clue words, such as series or character names, `juvenile` or `fiction`. The app checks them against the book's title and subjects. A row with no clues is that author's usual section. If nothing matches, the app leaves the section for a person to choose and says why.
  - Anonymous, Unknown and Various never get a rule, and those books sort by title.

You can also work in the *Stock* tab directly:

- **Edit** a title, section, genre or anything else, and the phones pick it up within a minute.
- **Add** a book by typing a new row with at least a title (an ISBN in the barcode column helps). Leave `id` blank: the script fills it in on the next sync, with a count of 1 unless you've typed a different one.
- **Remove** a book by deleting its whole row (right-click the row number → Delete row). It disappears from every phone, as if it was never there. Deleting a book in the app removes its row from the sheet the same way.
- Leave `id`, `updated` and `log` alone. You can add your own extra columns, and they'll be left untouched. If your sheet has an old `deleted` column, it's no longer used and you can delete it.
- The script adds two hidden tabs, `_ids` and `_removed`, to keep track of removals. Leave those be.

**Getting a newer built-in section list:** when the app is updated with new sections, use **Shop Stock → Load the updated section list (keeps a backup)**. Your current tab is renamed *Sections backup (date)*, the new list goes in, and books in sections that were renamed move across automatically. Anything you'd added yourself is still in the backup tab, ready to copy back in.

**After a big find-and-replace** (say you rename a section and update every book in it), use the **Shop Stock** menu at the top of the sheet → **Send sheet changes to the phones**. Find-and-replace doesn't always count as an edit, so this makes sure every phone catches up. The menu appears after you reload the sheet once.

If your sheet was set up with the first version's two-letter section codes, the new script renames that tab to *Old sections (codes)*, creates the new *Sections* tab, and moves existing books and author rules across to the full names automatically. You can delete the old tab afterwards.

**Turn on Google's text reader (about 2 minutes, once):**

1. In the Apps Script editor, click **Services** (the **+** next to it, in the left-hand column).
2. Choose **Drive API** and click **Add**.
3. Deploy a new version (below). Google asks you to authorise again, because the script now also creates and reads Google Docs. Go through **Advanced → Go to… → Allow**.

Each photo becomes a temporary Google Doc called *Shop Stock photo (temporary)* in your Drive for a second or two while it's read, then it's deleted. If one is ever left behind (for example if the connection drops mid-read), it's safe to delete.

**If you change `Code.gs` later:** go to **Deploy → Manage deployments**, click the pencil, choose **New version**, then **Deploy**. This keeps the same web app URL. A brand-new deployment would give a new URL, which every phone would need.

## Your data

- Everything is stored on the phone, in that browser, using IndexedDB. With Sharing switched on, it's also kept in your Google Sheet.
- **Back up** downloads a `.json` file with everything, history included. **Restore or import** loads it back in, on the same phone or a new one.
- **Download spreadsheet** gives a CSV for Excel or Google Sheets. Restore also accepts a CSV with the same column names, so data exported from the earlier Claude version can come across.
- The app nags you once there are 10+ items and you haven't backed up for two weeks.

## Updating

Edit `index.html` (on GitHub you can click the file, then the pencil icon), commit, and the site updates within a couple of minutes. Your data on the phone isn't affected.
