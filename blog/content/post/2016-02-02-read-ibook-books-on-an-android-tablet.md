---
title: Read iBook books on an Android tablet
author: Bill Farmer
type: post
date: 2016-02-02T16:59:51+00:00
url: /read-ibook-books-on-an-android-tablet/
categories:
  - Hacking
keywords:
  - calibre
  - epub
  - ibooks
  - itunes
---
You will need to install [Calibre][1] to make the books usable, and the books must be DRM free.

There are two ways to do this:

  * If you have a Mac or Hackintosh you can find your books in `~/Library/Containers/com.apple.BKAgentService/Data/Documents/iBooks/Books`, in what appears to be epub files but are really folders with numeric names. Copy the ones you want to somewhere accessible, like `~/Books`, and zip the contents of each folder, not the folder itself. Rename the zip files as epub files.

![iBooks][2]

  * Alternatively, use iTunes on a PC to download the books you want. You can find them by right clicking a book and selecting _Show in Windows Explorer_.

![iTunes][3]

Your books will be readable epub files with no front cover and no table of contents, despite having a file called `cover.jpg` and `toc.ncx` in them. Load them into Calibre, and use the _Edit book_ toolbar button to edit the book. Use the _Tools>Table of Contents>edit Table of Contents_ menu item, and press the _Generate ToC from major headings_ button. Use the _Tools>Add cover_ menu item and pick the `cover.jpg` item for the cover. Save the edits, and you now have a usable epub book to export to your tablet. Use Aldiko, or your favourite epub reader to read it.

![Calibre][4]

![Edit][5]

![ToC][6]

![Cover][7]

 [1]: http://calibre-ebook.com
 [2]: images/2016/02/iBooks.png
 [3]: images/2016/02/iTunes.png
 [4]: images/2016/02/Calibre.png
 [5]: images/2016/02/Edit.png
 [6]: images/2016/02/ToC.png
 [7]: images/2016/02/Cover.png
