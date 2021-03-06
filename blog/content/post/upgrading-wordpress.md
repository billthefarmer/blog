---
title: Upgrading WordPress
author: Bill Farmer
type: post
date: 2014-05-28T11:05:54+00:00
url: /upgrading-wordpress/
categories:
  - Hacking
keywords:
  - wordpress
  - upgrade
  - firewall
  - filezilla
---
This site has the firewall set up so that the web server has no external access. This means that I can&rsquo;t use the usual WordPress update mechanism to keep it up to date. I use [FileZilla](https://filezilla-project.org) for maintaining the server, and I recently discovered you can use drag and drop to move stuff around remotely.

So my easy WordPress upgrade method is:

  * Download the latest zip file and unzip it.
  * Rename the wordpress folder to something else, like wordpress-old.
  * Upload the new wordpress folder.
  * Move the stuff you need from the old wordpress-old folder, like themes, plugins, uploads, any modified theme files and your config.php file.
  * Check it works and upgrade the database.
  * Remove the old wordpress-old folder.

Nearly as easy as letting it do it itself.
