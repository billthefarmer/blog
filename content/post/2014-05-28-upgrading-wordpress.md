---
title: Upgrading WordPress
author: Bill Farmer
type: post
date: 2014-05-28T11:05:54+00:00
url: /upgrading-wordpress/
categories:
  - Hacking

---
This site has the firewall set up so that the web server has no external access. This means that I can&#8217;t use the usual WordPress update mechanism to keep it up to date. I use <a title="https://filezilla-project.org" href="https://filezilla-project.org" target="_blank">FileZilla</a> for maintaining the server, and I recently discovered you can use drag and drop to move stuff around remotely.

So my easy WordPress upgrade method is:

  * Download the latest zip file and unzip it.
  * Rename the wordpress folder to something else, like wordprezz.
  * Upload the new wordpress folder.
  * Move the stuff you need from the old wordprezz folder, like themes, plugins, uploads, any modified theme files and your config.php file.
  * Check it works and upgrade the database.
  * Remove the old wordprezz folder.

Nearly as easy as letting it do it itself.