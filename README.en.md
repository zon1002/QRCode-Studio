[繁體中文](README.md) | English

# QRBarcode Studio (Rust edition)

A QR Code and barcode generator and label printing tool written in Rust / egui (v4.1.0).
It combines the C# edition (v3.1) and the Python edition (v2.14): it keeps the useful features of both, fixes their known shortcomings, and adds label and paper layout, printing many cells on one page, and saved settings and templates.

**No extra packages are needed at install time**: it is a single `QRBarcodeStudio.exe` with the C runtime linked statically, so you do not need to install .NET, Python, or the Microsoft Visual C++ Redistributable.
The encoders for QR Code and all the barcodes were written from scratch in Rust, with no third-party barcode library. Fonts are loaded from the system, not embedded. Only Windows 10/11 x64 is supported.
The interface is in Traditional Chinese or English, chosen automatically from the Windows display language; see "Interface language".

This repository holds only the user guide and license documents, not the source code; download the installer and the executable from [Releases](https://github.com/zon1002/QRCode-Studio/releases/latest).

## Installation

| File | Description |
|------|------|
| `QRBarcodeStudio_Setup_v4.1.0.exe` | The installer (recommended); optional desktop shortcut and `.qrbjob` file association |
| `QRBarcodeStudio.exe` | A single executable that runs without installing; no Start menu shortcuts and no `.qrbjob` file association |

- The program has no digital signature, so the first time you run it Windows may show "Windows protected your PC"; after making sure the file came from the Releases page above, click "More info" → "Run anyway".
- **By default it installs for the current user only and needs no administrator rights**; during setup you can instead install it for all users of the computer.
- After installation there are Start menu shortcuts, including `User guide (README)`, `License agreement`, and `Third-party licenses`; the desktop shortcut is optional.
- **The installer's language** is chosen automatically from the Windows display language (Traditional Chinese or English, with no language prompt); the Start menu shortcut names, and which README the `User guide (README)` shortcut opens (`README.md` or `README.en.md`), follow the installer's language.
- **`.qrbjob` file association** (checked by default): opens `.qrbjob` job files with this app. Double-click a job file in File Explorer and the app opens and loads it.
- **If the app is already open, double-clicking a job file opens a second window**; the file is not sent to the first window.
  **Settings changed in the second window are not saved** (paper presets, label formats, theme, view, and so on). At startup the status bar says "Another QRBarcode Studio window is already open; settings changed in this window will not be saved, but templates and job files are saved as usual"; templates and job files are separate files and are saved as usual.
  This keeps the two windows from overwriting each other's settings (for example, a paper preset added in one window being overwritten when the other window closes). To change settings, do it in the window that was opened first, or close all windows and start the app again.
  The first window keeps `instance.lock` open in the settings folder; Windows releases it when the app exits (including when it crashes).
- **Uninstalling keeps your settings and templates**: only the program itself is removed, and `%APPDATA%\QRBarcodeStudioRS\` is not deleted. To remove everything, delete that folder yourself.
- Installing a new version overwrites the old one directly; settings and templates stay as they were.

## Features

You switch between the "QR Code" and "Barcode" modes for the whole app; each mode keeps its own settings when you switch.
The preview updates automatically whenever a parameter changes (it is generated in the background and debounced, so the screen does not stall while you type). When a field is filled in wrongly, the error appears right beside the field instead of in a pop-up window.

### QR Code

- **14 content types**: Plain text, URL, Wi-Fi, vCard contact, Email, SMS, Phone, Geolocation, Calendar event, Bitcoin, TOTP, Skype, MMS, Bookmark.
  Each type handles special characters as its specification requires (escaping for Wi-Fi, vCard, and calendar events; percent-encoding for Email; and so on). A badly formed URL gets a hint but is not blocked.
- **Encoding**: versions 1–40; error correction level L/M/Q/H (default M); numeric, alphanumeric, and byte (UTF-8) modes, automatically split into the shortest mix of modes; the mask is chosen automatically.
- **Style**: 6 module shapes (Square, Gapped squares, Circles, Rounded, Vertical bars, Horizontal bars); 6 color modes (Solid color, Radial gradient, Square gradient, Horizontal gradient, Vertical gradient, Color from background image); foreground color, background color, and a second gradient color.
  If you choose "Color from background image" without loading a background image, a note beside the field explains it; the app does not quietly fall back to a solid color.
- **Center logo**: size 10–40% (25% recommended), rounded corners, and a background box with its own color. When you load a logo, the "Error correction level" is changed to H automatically only if you have not clicked the "Error correction level" yourself; removing the logo does not change it back.

### Barcodes

- **19 types**:
  - 12 one-dimensional: Code 128, GS1-128, Code 39, Code 93, EAN-13, EAN-8, UPC-A, UPC-E, ITF, ITF-14, Codabar (NW-7), PZN.
  - 4 EAN-13 derivatives: ISBN-13, ISBN-10, ISSN, JAN (the input is converted and validated, then turned into an EAN-13 with the matching prefix).
  - 3 two-dimensional: Data Matrix, PDF417, Aztec.
- **Live input validation**: the number of digits, check digits, allowed characters, an even number of digits (ITF), and so on; errors appear beside the field. GS1-128 marks the AIs with parentheses, for example `(01)04712345678900(17)261231`.
- **Parameters**: module width, bar height, quiet zone, top and bottom margin (for 1D barcodes the top and bottom are not part of the quiet zone; the default is 0 mm), foreground and background colors, the text below the barcode (font size and gap), the character set for 2D codes, and long guard bars for EAN/UPC.
- What each type accepts, an example, and the recommended quiet zone are all in "All barcode types" in the Help window (F1); that table is generated from the program itself.

### Caption

QR Code and barcodes share one set of styles: the text source (typed manually or a column of the data), the system font, the font size (pt; 0 means automatic), the color, the alignment, the position (below, above, to the left, to the right), and the spacing above and below.
When the text is too wide it wraps onto at most N lines (default 2) and the font size is reduced step by step down to a minimum (default 6 pt). The background is the same as the barcode's background color.
"Include caption in QR content" is off by default and can be checked only when the content type is "Plain text". Every output format draws the caption; the vector formats (SVG, PDF, EPS) convert the text to outlines, so they do not depend on the other computer having the font.

### Data source

All sources fill the same data list. Batch export and printing use the list that is currently loaded, not the tab you are looking at.

- **Manual entry**: form input, one record.
- **Pasted lines**: one record per line.
- **Serial numbers**: generated from a rule, for example `A-0001~A-0100`; the prefix and suffix may be empty, numbers are zero-padded to the width of the start number, and you can set a step. More than 100,000 records ask for confirmation first.
- **Data file**:
  - Excel (xlsx, xls, xlsm, ods): you can choose the worksheet and check "First row is the header"; the file can be read even while Excel has it open.
  - CSV: the encoding (UTF-8 including BOM, UTF-16, Big5/CP950) and the delimiter (comma, Tab, semicolon) are detected automatically, and quotes and line breaks inside fields are handled correctly.
  - Every value is read as text: integers do not show a `.0`, empty cells are empty strings, and dates are `yyyy-MM-dd`.
- **Field mapping**: for QR types with several fields (Wi-Fi, vCard, Email, and so on), each field can be mapped in a batch to a column of the data, given a fixed value, or left empty; the caption and the "Quantity column" work the same way.
  The mapping is stored by column name in templates and job files and is applied automatically the next time you load a file with the same column names; column names that cannot be matched are listed so that you can map them again.
- **Data table**: 100,000 records still scroll smoothly. Records that cannot be generated and records with a quantity problem are marked. "Output range" lets you export only records N through M, and each data source remembers its own range.

### Output

- **Single image**: PNG, JPEG (quality 10–100), WebP (lossless), BMP, SVG, PDF, EPS, TXT (ASCII art or Base64); the format follows the file extension you type when saving.
  Bitmap formats write the DPI. Right-click the preview to copy the image (for ASCII and Base64 the text is copied).
- **Size** can be specified in two ways: "By module" sets the module size; "By frame" sets the width or height of the whole image (including the quiet zone and the caption), and the width or height you specify is the actual width or height of the whole image, with the barcode centered inside it, never stretched or squashed.
  When saving a bitmap and when printing, every module is aligned to a whole number of dots, so there are no gray blurry edges; the actual size and module size are shown next to the preview.
- **Batch export**: runs in the background, shows progress, and can be stopped at any time.
  - File name rule: `{index}`, `{index:04}` (zero-padded width), `{value}`, `{caption}`, and `{column name}`; invalid characters are replaced with `_`.
  - Files with the same name: "Add a number automatically" (the default), "Skip", or "Overwrite"; before it starts you are told how many conflicts there are.
  - A single failure does not stop the batch; when it ends, the number of successes, the number of failures, and a failure list are shown, and the list can be exported as a CSV.
  - You can also "Merge into one PDF by layout", arranging several cells on a page with the "Layout" settings.

### Layout and printing

- **Paper**: regular paper (A3, A4, A5, B4, B5, and so on), Brother DK continuous label tape (12, 29, 38, 50, 54, and 62 mm wide, with an adjustable length), and DK die-cut labels (10 sizes), 24 entries in all; you can add, edit, and delete them.
  Five A4 label sheet formats (3×8, 3×7, 2×7, 4×10, 5×13) apply the paper, cells, margins, and gaps all at once, and you can save your own frequently used formats.
- **Cells**: columns × rows or a specified cell size, margins, horizontal and vertical gaps, the start cell (so you can continue on a partly used sticker sheet), cut lines, and the arrangement within a cell.
- **Quantity and copies**: the quantity per record (you can also use a "Quantity column" so that each record is printed a different number of times), "Fill the whole page" (each record starts on a new page and fills it), and the number of copies.
- **Printing** (Windows GDI): lists all printers and preselects the Windows default printer. The paper is matched against the driver's own paper list first, a custom size is tried only if that fails, and when neither works the driver's papers are listed for you to pick. The whole job is one print job, and you can print only some pages.
  The printed size equals the configured mm, minus the edges the printer physically cannot print on.
- **Print preview**: shows the actual image that is sent to the printer, draws the non-printable edges and an mm ruler, so you can check the position and size first. Exporting a PDF ("Merge into one PDF by layout") uses the same layout as printing, and the PDF page size is exactly the configured mm.
- Preview, saving, batch export, full-page preview, the merged PDF, and printing all go through the same path, so what you see, what you save, and what is printed are the same thing.

### Templates and settings

- **Templates**: store the complete design, layout, output settings, field mapping, quantity per record, "Fill the whole page", and copies, but not the data itself.
  Each template is a `.json` file and can be imported and exported; the logo and background image are embedded as base64, so a template also works when copied to another computer. When one template is damaged, only that one is skipped.
  6 built-in templates: 62×29 product label, A4 24-label sheet, Wi-Fi sharing, vCard business card, EAN-13, and URL QR; they can be edited or deleted.
- **Settings**: remembers the current view (E or C) and the page you were on, the window position and size, the theme (light or dark), the last generation parameters, recently opened data files, the last output folder, and the paper presets.
- **Job file locks**: the app itself has no password. To give a job file to a coworker without letting it be changed by accident, you can lock some of "Content", "Data source", "Caption", "Style", and "Layout" and set a password when you save; see "Job file locks".

### Help

Press F1 or click "Help" at the far right of the status bar to open the "QRBarcode Studio Help" window, with the topic list on the left and the content on the right; it works in both views.
The parts of the help that could easily disagree with the program (barcode types, examples and recommended quiet zones, content types, keyboard shortcuts, job file locks) are generated from the program itself. The main fields have tooltips, and every scrolling area works with the mouse wheel.

## The two views

The two views share the same settings, data, and templates, so switching loses no input. At the far right of the toolbar at the top there are two buttons, "E view" and "C view"; the current view is the selected one. Click the other one to switch at once, with no need to restart the app and no password. The first time the app opens in the E view, and after that it returns to the view you used last.

| Code | Name | Who it is for | Description |
|------|------|--------|------|
| E | Side navigation bar | Everyday personal use | On the left, in working order, are "File", "Data", "Content", "Style", "Caption", "Layout", and "Output"; at the bottom are "Templates" and "Settings". On the right is the live preview, which can switch between "Single" and "Full page" |
| C | Label layout designer | Laying out A4 sticker sheets, using a label printer | The middle is a full-page preview drawn at the paper's real proportions; click a cell to see its record. The data source is on the left, and the settings you can expand are on the right |

## Interface language

The interface is in Traditional Chinese or English. The language is decided once, when the app starts, from the Windows "display language"; there is nothing to set and no switch button:

- When the display language is **Traditional Chinese** (Taiwan, Hong Kong, Macao), the interface is in Traditional Chinese.
- When the display language is any other language (including Simplified Chinese and Japanese), the interface is in English.
- To see the English interface on a Chinese Windows (or the other way around), set the environment variable `QRBARCODE_LANG`: the value is a language tag such as `en`, `en-US`, `zh-TW`, or `zh-Hant`, and it is not case-sensitive. An empty or unrecognized value is ignored, and the Windows language is used.
  To start the app in English from PowerShell: `$env:QRBARCODE_LANG = "en"; .\QRBarcodeStudio.exe`; in a Command Prompt, run `set QRBARCODE_LANG=en` first and then start the app from the same window.

The whole interface follows the language: the screens, the Help window, error messages, the print preview, and the results of a batch export (including the header of the failure list CSV). Numbers, dates, and units (mm, pt, dpi) do not change.
Settings, templates, and job files work in both languages: a job file saved in one language can be opened in the other, and its field mapping, the built-in papers, and the caption fonts all keep working.
The built-in papers, label formats, and templates are named in the language in use the first time the app runs and are then saved; switching languages later does not rename them or add duplicates.

## Job files

A job file (extension `.qrbjob`) saves "this print job" as a file, so you can reprint it exactly as it was, or take it to another computer or hand it to a coworker. In the "File" tab (in the C view, "File and printing") click "Open job file…" or "Save job file…".

- **What is stored**: the design (including the logo and background image), the layout, the output settings, the field mapping, the quantity per record, "Fill the whole page", the copies, the data source and output range, plus the printer, paper, and page range; if the job file is locked, also the locked parts and the hash of the password.
- **When the data source is pasted lines or serial numbers**, the pasted content or the serial rule is stored directly in the job file. **For Excel and CSV only the file path and the import settings are stored**, and the file is read again when the job file is opened, so if the data file has changed, what prints is the new content.
- **When the data file cannot be found**: the app first tries the stored original path, then a file with the same name in the folder that holds the job file; if neither is found, it shows the original file name and path, and you click "Browse…" to locate it. Until it is found you cannot preview, print, or export, so the wrong data is never printed. If you keep the job file and the data file in the same folder, they can still be found after you move them to another computer.
- When there are unsaved changes, the window title gets a "*", and the app asks whether to save before you open another job file or close the app.
- Double-clicking a job file in File Explorer opens it (see the `.qrbjob` file association in "Installation").
- When you save, you can lock some parts of the job file; see the next section, "Job file locks".

## Job file locks

The app itself has no password; the lock travels with the job file. A job file for your own use is not locked and needs no password; only when you share a job file with a coworker do you decide what to lock and set a password on that one file.
The lock is meant to prevent a coworker from accidentally changing the layout or style, not to be a security measure: there is no limit on password attempts, and anyone who edits the job file in Notepad can remove it (see "Forgot the job file password").

### How to set one

1. Click "Save job file…" (or press Ctrl+Shift+S; when there is no job file yet, Ctrl+S does the same). The "Save job file" window appears first. When a job file is already open, Ctrl+S saves straight back over it without showing this window, and the lock and password are kept as they are.
2. Check "Lock before sharing with others". It is unchecked by default: unchecked means no lock and no password, just like a normal save.
3. After you check it, choose the parts to lock: "Lock Content", "Lock Data source", "Lock Caption", "Lock Style", and "Lock Layout". "Lock Layout" and "Lock Style" are checked by default, and at least one must be checked. The parts are locked separately; for example, if "Style" is locked, "Caption" can still be changed.
4. Enter the "Password" and "Confirm password". If the password is blank or the two do not match, a message below the field explains why and the window stays open. The password is stored in the job file as a salted Argon2id hash, never in plain text.
5. Click "Continue" to open the save dialog; click "Cancel" to save nothing.

### What gets locked

| Part | Items that are grayed out and cannot be changed |
|------|------|
| Content | The content type and the input fields in the "Content" tab (for barcodes, "Barcode content" and the "Add-on" of EAN/UPC), and "Include caption in QR content" |
| Data source | The data source choice in the "Data" tab (Manual entry, Pasted lines, Serial numbers, Data file), choosing the file, the worksheet, the field mapping, the output range, and the serial rule |
| Caption | All settings in the "Caption" tab (in the C view, "Caption settings") |
| Style | All settings in the "Style" tab (in the C view, "Style settings"), plus the "QR Code" and "Barcode" switch in the toolbar, and the barcode type and encoding options in the "Content" tab (error correction level, minimum version, check character, Full ASCII, and so on), because these all change the size and appearance of the barcode |
| Layout | The A4 label format, paper settings, cells, and cell arrangement in the "Layout" tab (in the C view, "Layout settings") |

- **Each row of the field mapping** follows the part it belongs to: the rows of content fields belong to "Content", the caption source row belongs to "Caption", and the quantity column belongs only to "Data source". A row cannot be changed if the part it belongs to or "Data source" is locked.
- **"Include caption in QR content"**: when it is checked, the caption is encoded into the QR Code together with the content and becomes part of the scanned content. To keep the scanned content from being changed, lock "Caption" as well as "Content".
- **Never locked** (a coworker must always be able to print): in the "File" tab, the printer, copies, page range, "Print preview…", "Print", and "Use the printer's paper"; in the "Layout" tab, the "Quantity" section and "Start cell"; the "Output" tab and batch export; the "Settings" tab; and "Browse…" when the data file cannot be found, "Worksheet to use" when the recorded worksheet is missing, and "Regenerate serial numbers" after serial number generation was canceled on opening.
- **Applying a template** (the "Templates" tab in the E view, the "Templates" menu in the toolbar of the C view): disabled as soon as any part is locked, with the explanation "This job file has locked parts, so a template cannot be applied.", because a template replaces the whole design, field mapping, and layout. Saving, exporting, importing, and deleting templates are not affected.

### When a coworker opens a locked job file

- The locked parts are grayed out in both the E view and the C view and cannot be clicked or typed into. Hover over one and the reason appears, for example `This job file has "Style" locked; unlock it first to make changes.`; the other parts work as usual, and printing works as usual.
- Above the preview there is a notice, for example `This job file has "Style", "Layout" locked; to make changes, click "Unlock…".` (it lists the parts that are actually locked, in the order "Content", "Data source", "Caption", "Style", "Layout"), with an "Unlock…" button next to it.
- Click "Unlock…", enter the "Job file password" in the "Enter the job file password" window, and click "OK". If the password is wrong, the window says "The password is incorrect; try again", stays open, and there is no limit on attempts. Once the password is correct, you can change the locked parts for as long as this job file stays open; the notice changes to "This job file is unlocked; the lock is still kept when you save it." and the button becomes "Lock again".
- **Unlocking lasts only while this job file stays open**: after "Lock again", after opening another job file, or after closing the app and opening this job file again, it is locked once more.
- **The lock is kept when you save back**: after you change an unlocked part (or change a locked part after unlocking) and save, with Ctrl+S or with "Save" when the app asks whether to save before closing, the lock and the password are written back as they were.
- **Saving a copy**: while the job file is still locked, the "Save job file" window only shows the current lock and says "This job file is locked; to change what is locked or the password, unlock it first."; the lock goes along unchanged with the copy you save. After unlocking, you can change what is locked, change the password (if both password fields are left blank, the original password is kept), or uncheck "Lock before sharing with others" to remove the lock.
- **The person who sets the lock** stays unlocked after saving, for as long as the job file stays open, and does not need to enter the password just set; the job file is locked when it is opened again.
- **A job file with a damaged lock** (an incomplete recorded lock) does not keep the job file from opening: the app treats it as having no lock, the status bar says "This job file's lock is incomplete, so it was opened as if it had no lock.", and after you save, the damaged lock no longer exists.

### Locked job files are not recorded in the app settings

While a locked job file is open (whether or not you have unlocked it), the design, layout, field mapping, quantity, copies, data source, and output settings in `settings.json` stay as they were before you opened that job file; the app's own settings, such as the theme, the current view (E or C), and the paper presets, are saved as usual.
The next time you start the app directly (not by double-clicking a job file), the screen returns to how it was before the job file was opened; after you open a job file without a lock, the app goes back to remembering your settings the usual way. The same is true when you set a lock yourself and save: from the moment you set the lock, later changes are not recorded in the app settings, so save them back to the job file to keep them.

## Keyboard shortcuts

| Key | Action |
|------|------|
| Ctrl+S | Save job file: if a job file is open, save over it directly; otherwise ask where to save |
| Ctrl+Shift+S | Save job file under a new name |
| Ctrl+O | Open job file |
| Ctrl+P | Print: same as "Print" in the "File" tab |
| Ctrl+C | Copy the preview image; when the cursor is in a text box, copy text as usual |
| Ctrl+E | Batch export: same as "Start export…" |
| F1 | Open Help |

- A shortcut does exactly what the matching button on the screen does; when the button cannot be clicked, the shortcut does nothing either, and the status bar explains why.
- Ctrl+O opens a **job file**, not a data file (open data files in the "Data" tab). Ctrl+P prints directly, like clicking "Print", without opening the print preview first.
- When Ctrl+S saves over a job file that is already open, the lock and the password are kept as they are; Ctrl+Shift+S, and Ctrl+S when there is no job file yet, first show the "Save job file" window (see "Job file locks").
- While the "Enter the job file password" window, the "Save job file" window, the "Print preview" window, or a window asking whether to save is open, only F1 has any effect.

## Where files are stored

Settings and templates are kept in `%APPDATA%\QRBarcodeStudioRS\` (the name ends in RS so that it is not mixed up with the earlier editions); the "Settings file location" on the "Settings" page shows this folder and has an "Open folder" button.

| Path | Contents |
|------|------|
| `settings.json` | Settings: the current view (E or C), the theme, the last generation parameters, recent data files, and the paper presets; while a locked job file is open, the generation parameters stay as they were before it was opened (see "Job file locks") |
| `templates\` | Templates, one `.json` file per template |
| `window.ron` | Window position and size |
| `instance.lock` | The lock file held by the first window that is open: a window that cannot get it (a second window) does not save settings, see "Installation"; it is released when the app exits, and the file itself has no content |
| `settings.json.bad` | The backup left when the settings file is damaged or cannot be read; if this file already exists, a date and time is added to the name (`settings.json.<yyyyMMdd-HHmmss>.bad`), so older backups are never overwritten |

Settings are written to a `.tmp` file first and then renamed, so a crash halfway through cannot corrupt them. When reading fails, the old file is renamed to `.bad` and kept, and the app starts with the default values and says so in the status bar.

## Forgot the job file password

A job file stores only a hash of the password, so a forgotten password cannot be recovered. The Help window only points to this README, so the steps are here:

If you forget the password: Close the program, open the .qrbjob file in Notepad, delete the "lock" section (from `"lock": {` to its matching `}`, and mind the comma at the end of the line before it), then save the file.

In detail, `"lock"` is the last section of the file and looks like the following. Delete all the lines from `"lock": {` to its matching `}`, then remove the comma at the end of the previous line (the `}` that closes the `"print"` section), so that the file is still valid JSON:

```json
  "print": {
    ...
  },
  "lock": {
    "parts": [
      "layout",
      "style"
    ],
    "password": "$argon2id$..."
  }
}
```

After you save, open the job file with the app again: it is now a job file without a lock, and everything else in it is unaffected. This is a lock to prevent accidental changes, not a security measure, so removing it needs no special tools; editing one job file removes the lock of that one file only, and other locked job files are not affected.

## Excel notes

Excel's number formats strip leading zeros (for example, 00123 becomes 123), so format that column as text before you save the file.

## Differences from the earlier versions

- **Settings are saved**: neither earlier version saves any settings; the Rust edition remembers the view, the parameters, the window position, and recent data files, and can also save named templates. When the settings file is damaged it is renamed to `.bad` and kept, never silently reset.
- **The screen does not freeze**: the preview is generated in the background and debounced; batch export runs in the background, with progress and a cancel option, instead of redrawing on the interface thread at every keystroke.
- **Correct sizes**: modules are aligned to whole dots, with no more gray edges from scaling, and specifying a width and height does not stretch the image; PDF pages follow the mm setting (the Python edition's page size was about 4 times too large); printing no longer enlarges to fill the paper, and the printed size equals the configured mm.
- **File formats**: WebP is real WebP (the Python edition wrote a PNG); EPS is written directly as vector and no longer depends on Inkscape; each format has its own encoder and all of them draw the caption and the logo; XAML is no longer produced.
- **Correct content**: special characters in Wi-Fi, mailto, vCard, and calendar events are escaped; the QR caption is no longer always written into the encoded content but is an option (off by default); barcode content is checked as you type.
- **Data**: Excel no longer shows `1234.0` or `nan` (for leading zeros see "Excel notes"); Excel can be read while it is open; Big5 CSV is no longer garbled and line breaks inside fields are not split wrongly; the serial rule can have an empty prefix or suffix, can set a step, and reports a clear error when the start is greater than the end; the output range is remembered separately for each data source; batch export and printing use the list that is currently loaded.
- **Batch**: files with the same name are never silently overwritten; a single failure does not stop the whole batch; there is a failure list that can be exported.
- **Help**: the help content is generated from the program itself, which fixes the places where the old help disagreed with the real behavior (for example the number of barcode types, the recommended quiet zone widths, and what "Auto" means for the QR version).
- **Interface**: a native window frame that snaps to the screen edges, and a maximized window does not cover the taskbar; keyboard shortcuts, and every scrolling area works with the mouse wheel; error messages are rewritten as sentences that say what happened and what to do; every setting in the interface really does something.
- **Not in this version**: scanning and decoding barcodes, XAML output, and macOS/Linux.

## Third-party components

The third-party components this app uses (packages, versions, licenses, and URLs) are listed in `THIRD_PARTY_NOTICES.md`, and the full license text of each component is in `THIRD_PARTY_LICENSES.txt`; both files are placed in the installation folder and are also in this repository.
The license text comes first from the license file shipped inside each package; for the few packages that do not ship one, the standard text is used, with the package's authors written in. The Rust standard library compiled into the program is also listed.

### License and trademarks

This app is commercially licensed. The terms are in `LICENSE.txt` (in Chinese and English; if the two disagree, the Chinese text prevails). You must accept them before installing, and they can also be viewed from the installation folder and from the "License agreement" item in the Start menu:

- Personal use for home, study, or other non-profit purposes is free.
- Use in the business of a company, firm, agency, school, or other organization, or use for profit, is commercial use and requires buying a commercial license from the copyright holder beforehand (send an email to johnsonjs@gmail.com).
- You may not sell, rent, or distribute the program, nor reverse engineer or modify it.

Copyright (C) 2026 johnsonjs@gmail.com. All rights reserved.

QR Code is a registered trademark of DENSO WAVE INCORPORATED.
