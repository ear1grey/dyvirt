DyVirt - Dynamic Virtual Hosting
==

Herein is a description of the files and configurations I use to enable dynamic virtual hosting on an OSX machine running Apache Web Server.

The result of using this method is that the task of adding a new web server at `http://example.dev` requires only the creation of a folder calld `example`.

Apache Vitual Hosting
--
Two things need configuing, the first is _dynamic virtual hosting_ which is controlled via the `httpd-vhosts.conf` file.  This file is commonly found in the `apache/conf/extras` folder (or in `conf/apache/extras` on MAMP).

It is common for this file to be ignored in Apache installs, so you may need to search in your `httpd.conf` file and uncomment the line that:

	`Include <FULL_PATH_TO_APACHE>/extra/httpd-vhosts.conf`

DNS
--
There are several ways to handle domain name resolution.  The simplest (but less automated) is to add example.dev to the /etc/hosts file.  A better way is to run a local DNS server so that a wildcard DNS entry can be created such that anything that matches `*.dev` will resolve to `127.0.0.1` (or `::1` if you're going down the IPv6 route).

I use `dnsmasq` which can be installed via homebrew (`brew install dnsmasq`).

Thereafter edit `/usr/local/etc/dnsmasq.conf` and add the line:
	`address=/dev/127.0.0.1`

Run dnsmasq and configure your TCP/IP stack to use this in addition to your other DNS servers.

If all configured correctly, example;dev should resolve to localhost, so visiting http://example.dev shoudl serve the files in your example folder.
  