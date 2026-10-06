# Shop Stock

A phone-first stock tracker for a secondhand bookshop. It scans barcodes, reads publisher pages, counts books in and out, and remembers everything you've ever had.

It runs entirely in your phone's browser. There's no server, no account and no cost.

## Put it on GitHub Pages

1. Create a new repository on GitHub, for example `shop-stock`. It can be public or private (private Pages needs a paid plan).
2. Upload everything in this folder: `index.html`, `manifest.webmanifest`, `icon-180.png`, `icon-512.png` and this README. On GitHub that's **Add file → Upload files**.
3. Go to **Settings → Pages**. Under *Build and deployment*, choose **Deploy from a branch**, pick `main` and `/ (root)`, then **Save**.
4. After a minute or two the site appears at `https://<your-username>.github.io/shop-stock/`.
5. Open that address on your phone, then add it to your home screen:
   - **iPhone (Safari):** Share → Add to Home Screen.
   - **Android (Chrome):** ⋮ menu → Add to Home screen / Install app.

Opening it from the home-screen icon is worth doing. It runs full-screen, and on iPhone it stops Safari clearing the saved data when you haven't visited for a while.

## How it finds book details

- **Barcodes** are scanned live with the camera (ZXing).
- **ISBNs** are looked up on Open Library, then Google Books. Both are free and need no key.
- **Publisher pages and covers** are read on the phone itself with Tesseract (text recognition). The first time, it downloads about 10 MB of language data, which the browser then keeps. It pulls out any ISBN (including old 9-digit SBNs), the publisher, the "first published" year and the edition. With no ISBN, it searches Open Library using the words it read and lets you pick the match.
- **Genre and section** are suggested from the subjects the book databases return. To change which authors count as Major Thrillers, edit the `MAJOR_THRILLERS` list near the top of `index.html`. Your shop's section codes are in `SECTIONS`, just above it.

## Your data

- Everything is stored on the phone, in that browser, using IndexedDB. It doesn't sync between devices.
- **Back up** downloads a `.json` file with everything, history included. **Restore or import** loads it back in, on the same phone or a new one.
- **Download spreadsheet** gives a CSV for Excel or Google Sheets. Restore also accepts a CSV with the same column names, so data exported from the earlier Claude version can come across.
- The app nags you once there are 10+ items and you haven't backed up for two weeks.

## Updating

Edit `index.html` (on GitHub you can click the file, then the pencil icon), commit, and the site updates within a couple of minutes. Your data on the phone isn't affected.
