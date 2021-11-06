---
title: Character set
date: 2021-11-03T21:12:12Z
anchor: charset
weight: 262
---

![Charset][1]

The current character set is displayed under the current file name. The character set is detected when a new file is read. It may be changed by selecting the Charset item in the menu, which shows the current character set. 

#### Default
The default character set is set to UTF-8 on selecting New from the toolbar. It may be changed in the menu. Android defaults to UTF-8, don't use anything else unless you are sure what you are doing.

#### Detection
The current character set is detected on reading a file by the detection code from International Components for Unicode. If there are no accented characters or symbols in the text to give the detection algorithm something to work on it may not get it right.

#### Saving
Files will be saved using the current character set. To change it, use the Charset item in the menu, which shows the current set.

Caution &ndash; If you add accented characters or symbols to the text, make sure to check the current character set before you save it.

#### Use mode line
The character set may be set to UTF-8 by using a mode line in the text.
```
# ed: cs:u
```
See [Mode Line][2]

 [1]: images/Editor-charset.png
 [2]: #mode-line
