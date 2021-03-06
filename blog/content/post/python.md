---
title: Python
author: Bill Farmer
type: post
date: 2013-12-21T18:54:52+00:00
url: /python/
categories:
  - Hacking
keywords:
  - python
  - smtp
  - raspbian
  - raspberry
  - pi
  - crontab
---
I&rsquo;ve studiously avoided Python up till now, even though it appears to be fairly popular, because it violates the first law of compiler design: &lsquo;Blank space shalt not be significant except to separate tokens where necessary.&rsquo;. That&rsquo;s why you can minimise a lengthy JavaScript script like [jQuery][1] to one enormously long line and it still works just fine.

But, she-who-shall-be-ignored (self categorisation) decided she wanted to enter an advent calender competition which involved checking a web page to see if your name has come up and sending an email if it does. So I thought that&rsquo;s an ideal little job to run on my [Raspberry Pi][2]. So I installed the ssmtp package and attempted to test sending an email. After several days of faffing about with no joy I gave up.

One of the things that comes front and centre with the standard [Raspbian][3] installation is Python. So, after checking the documentation for smtp and http libraries, etc, I decided to give it a go. You can test your code on the command line before sticking it in a file, and you get, unlike ssmtp, helpful messages that tell you what&rsquo;s going on. So you end up with a script that looks like this, and it actually appears to work.

```python
#! /usr/bin/env python

import urllib2
import smtplib
import os.path
from os import utime
from datetime import date
from email.mime.text import MIMEText

if os.path.exists("sent")
  quit()

f = urllib2.urlopen("url")

today = False
for line in f:
  if "winners" in line:
    today = str(date.today().day) in line
    break

line = f.readline()

f.close()

if today and "name" in line:
  s = smtplib.SMTP_SSL("smtp.mail.yahoo.com")
  s.login("user","password")
  msg = MIMEText("I would like to claim my prize\r\n\r\nRegards\r\nname")
  msg['Subject'] = "Advent Calendar Competition"
  msg['From'] = "from email"
  msg['To'] = "to email"
  msg['Bcc'] = "from email"
  s.sendmail("from email", ["to email","from email"], msg.as_string())
  s.quit()
  print(msg.as_string())
  open("sent", "w").close()

with file("last", "a"):
  utime("last", None)
```

So, very careful with the indenting. We shall have to see if anyone wins anything. The script gets fired off periodically with a crontab entry.

```shell
CRON_TZ=Europe/London
MAILTO=bill
*/1 9-10 1-24 12 * $HOME/advent/advent.py
```

 [1]: http://jquery.com
 [2]: http://www.raspberrypi.org
 [3]: http://www.raspberrypi.org/downloads

