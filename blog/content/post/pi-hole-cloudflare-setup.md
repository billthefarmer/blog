---
title: "Pi-hole Cloudflare Setup"
author: Bill Farmer
type: post
date: 2019-06-17T18:30:39+01:00
url: /pi-hole-cloudflare-setup/
categories:
  - Hacking
keywords:
  - adblock
  - pi-hole
  - raspbian
  - raspberry
  - pi
---

I came across a reference to [Pi-hole][1] recently, so as my old pi
has been gathering dust, I thought I would try it out. The pi already
had [Raspbian Stretch Lite][2] installed, so I uninstalled some of the
packages I had previously installed, and loaded Pi-Hole using the
[One-Step Automated Install][3].

The installation script asks a series of questions using text dialogs
and produces a log as it goes.

```log
pi@catherine:~ $ curl -sSL https://install.pi-hole.net | bash
stty: 'standard input': Inappropriate ioctl for device

  [✗] Root user check
  [i] Script called with non-root privileges
      The Pi-hole requires elevated privileges to install and run
      Please check the installer for any concerns regarding this requirement
      Make sure to download this script from a trusted source

  [✓] Sudo utility check
stty: 'standard input': Inappropriate ioctl for device

  [✓] Root user check

  [i] Existing PHP installation detected : PHP version 7.0.33-0+deb9u3
  [✓] Disk space check
  [✓] Update local cache of available packages

  [✓] Checking apt-get for upgraded packages... up to date!

  [i] Installer Dependency checks...
  [✓] Checking for apt-utils
  [✓] Checking for dialog
  [✓] Checking for debconf
  [✓] Checking for dhcpcd5
  [✓] Checking for git
  [✓] Checking for iproute2
  [✓] Checking for whiptail

  [i] Using interface: eth0
  [i] Using Cloudflare
  [i] Your static IPv4 address: 192.168.1.252
  [i] Your static IPv4 gateway: 192.168.1.254
  [✓] Set IP address to 192.168.1.252
  You may need to restart after the install is complete
  [i] IPv4 address: 192.168.1.252
  [i] IPv6 address:
  [i] Web Interface On
  [i] Web Server On
  [i] Logging On.
  [✗] Check for existing repository in /etc/.pihole
  [✓] Clone https://github.com/pi-hole/pi-hole.git into /etc/.pihole

  [✗] Check for existing repository in /var/www/html/admin
  [✓] Clone https://github.com/pi-hole/AdminLTE.git into /var/www/html/admin

  [i] Main Dependency checks...
  [✓] Checking for cron
  [✓] Checking for curl
  [✓] Checking for dnsutils
  [✓] Checking for iputils-ping
  [✓] Checking for lsof
  [✓] Checking for netcat
  [✓] Checking for psmisc
  [✓] Checking for sudo
  [✓] Checking for unzip
  [✓] Checking for wget
  [✓] Checking for idn2
  [✓] Checking for sqlite3
  [✓] Checking for libcap2-bin
  [✓] Checking for dns-root-data
  [✓] Checking for resolvconf
  [✓] Checking for libcap2
  [✓] Checking for lighttpd
  [✓] Checking for php7.0-common
  [✓] Checking for php7.0-cgi
  [✓] Checking for php7.0-sqlite3

  [✓] Enabling lighttpd service to start on reboot...

  [i] FTL Checks...

  [✓] Detected ARM-hf architecture (armv6l)
  [i] Checking for existing FTL binary...
  [✓] Downloading and Installing FTL
  [✓] Creating user 'pihole'
  [✓] Installing scripts from /etc/.pihole

  [i] Installing configs from /etc/.pihole...
  [i] Existing dnsmasq.conf found... it is not a Pi-hole file, leaving alone!
  [✓] Copying 01-pihole.conf to /etc/dnsmasq.d/01-pihole.conf

  [i] Installing blocking page...
  [✓] Creating directory for blocking page, and copying files
  [✗] Backing up index.lighttpd.html
      No default index.lighttpd.html file found... not backing up

  [✓] Installing sudoer file

  [✓] Installing latest Cron script

  [✓] Installing latest logrotate script
  [i] Backing up /etc/dnsmasq.conf to /etc/dnsmasq.conf.old

  [i] Skipping firewall configuration
  [✓] man pages installed and database updated
  [i] Testing if systemd-resolved is enabled
  [i] Systemd-resolved is not enabled
  [✓] Restarting lighttpd service...
  [✓] Enabling lighttpd service to start on reboot...
  [i] Restarting services...
  [✓] Enabling pihole-FTL service to start on reboot...
  [✓] Restarting pihole-FTL service...
  [✓] Deleting existing list cache
  [i] Pi-hole blocking is enabled
  [i] Neutrino emissions detected...
  [✓] Pulling blocklist source list into range

  [i] Target: raw.githubusercontent.com (hosts)
  [✓] Status: Retrieval successful

  [i] Target: mirror1.malwaredomains.com (justdomains)
  [✓] Status: Retrieval successful

  [i] Target: sysctl.org (hosts)
  [✓] Status: Retrieval successful

  [i] Target: zeustracker.abuse.ch (blocklist.php?download=domainblocklist)
  [✓] Status: Retrieval successful

  [i] Target: s3.amazonaws.com (simple_tracking.txt)
  [✓] Status: Retrieval successful

  [i] Target: s3.amazonaws.com (simple_ad.txt)
  [✓] Status: Retrieval successful

  [i] Target: hosts-file.net (ad_servers.txt)
  [✓] Status: Retrieval successful

  [✓] Consolidating blocklists
  [✓] Extracting domains from blocklists
  [i] Number of domains being pulled in by gravity: 135697
  [✓] Removing duplicate domains
  [i] Number of unique domains trapped in the Event Horizon: 113438
  [i] Nothing to whitelist!
  [i] Number of regex filters: 0
  [✓] Parsing domains into hosts format
  [✓] Cleaning up stray matter

  [✓] Force-reloading DNS service
  [✓] DNS service is running
  [i] Pi-hole blocking will be enabled
  [i] Enabling blocking
  [✓] Reloading DNS service
  [✓] Pi-hole Enabled
  [i] Web Interface password: ********
  [i] This can be changed using 'pihole -a -p'

  [i] View the web interface at http://pi.hole/admin or http://192.168.1.252/admin

  [i] You may now configure your devices to use the Pi-hole as their DNS server
  [i] Pi-hole DNS (IPv4): 192.168.1.252
  [i] If you set a new IP address, please restart the server running the Pi-hole

  [i] The install log is located at: /etc/pihole/install.log
Installation Complete!
```

My router uses the IP address `192.168.1.254` and allocates [DHCP][4]
IP addresses from `192.168.1.1` and does not allow DHCP
configuration. I opted to use the ethernet port and use IP address
`192.168.1.252` and the Cloudflare DNS service. The router does allow
DNS configuration, so I changed the default DNS to `192.168.1.252` so
I don't have to make any changes to DNS configuration elsewhere.

There is a mention in the [Pi-hole docs][5] of [Configuring
DNS-Over-HTTPS on Pi-hole][6] using [Cloudflare][8]. This is useful to
stop your [ISP][7] from snooping on your browsing habits. However, the
latest version of [cloudflared][8] downloaded from their
[Downloads][9] page crashes instantly when run on my old Pi 1B. Having
looked at the cloudflared issues on [GitHub][10], others have had this
problem and used an older version which did work on an early Pi.

I couldn't find any links to earlier versions, so I installed the
[Go][11] compiler on the Pi and attempted to build the latest version
of the cloudflared. This failed, and having looked again at the
cloudflared issues, I found I needed a later version of the compiler
than came with Rasbian. So I downloaded the latest version of the
compiler from the [Go Downloads][12] page, installed it and
successfully built cloudflared.

This version stopped working after a day due to running out of file
handles. I looked at the `Makefile` that came with the cloudflared
sources and decided to build the latest released version.

```shell
pi@jennifer:~ $ cd gocode/src/github.com/cloudflare/cloudflared/
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ git checkout 2019.5.0
Note: checking out '2019.5.0'.
...
HEAD is now at 4bff1ef... Release 2019.5.0
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ export PATH=$PATH:/usr/local/go/bin
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ export GOPATH=~/gocode
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ go clean
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ make cloudflared
go build -v -ldflags='-X "main.Version=2019.5.0" -X "main.BuildTime=2019-06-03-1717 UTC"' github.com/cloudflare/cloudflared/cmd/cloudflared
pi@jennifer:~/gocode/src/github.com/cloudflare/cloudflared $ ./cloudflared -v
cloudflared version 2019.5.0 (built 2019-06-03-1717 UTC)
```

This version ran for about a week before failing with the same error.
I used the [Wayback Machine][13] to find an [earlier version][14] of
cloudflared that might run on the Pi 1B. This turned out to be version
2018.4.6. I have that version on soak test as third DNS choice in
Pi-hole, so it may take a while to fail if it is going to.

 [1]: https://pi-hole.net
 [2]: https://www.raspberrypi.org/downloads/raspbian
 [3]: https://github.com/pi-hole/pi-hole/#one-step-automated-install
 [4]: https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol
 [5]: https://docs.pi-hole.net
 [6]: https://docs.pi-hole.net/guides/dns-over-https
 [7]: https://en.wikipedia.org/wiki/Internet_service_provider
 [8]: https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy
 [9]: https://developers.cloudflare.com/argo-tunnel/downloads
 [10]: https://github.com/cloudflare/cloudflared/issues
 [11]: https://golang.org
 [12]: https://golang.org/dl
 [13]: https://web.archive.org/web/20180524132333/https://developers.cloudflare.com/argo-tunnel/downloads
 [14]: https://web.archive.org/web/20180419005946/https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-linux-arm.tgz
 
