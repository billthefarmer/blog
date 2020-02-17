---
title: Cursor Position
date: 2020-02-17T09:36:25Z
anchor: cursor
weight: 370
---

You may put a line in an entry to control or remember the edit cursor
position. This will not appear in the markdown view. Put `[<]: #` on a
line for the start of an entry, `[>]: #` for the end of an entry. Put
`[#]: #` for the cursor position to be remembered. There should be no
other text on the line. The current cursor position will be added when
the entry is saved `[#]: # (nnn)`. Because entries are only saved if
they are changed, moving the cursor with no other change will not move
the saved position.

**Note** &ndash; Using this functionality will not stop reference type links
to the top of the current entry, `[Top][#]` working. However you can
more simply use an inline link `[Top](#)`.
