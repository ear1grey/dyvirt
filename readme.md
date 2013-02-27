DyVirt - Dynamic Virtual Hosting
==

Herein is a description of the files and configurations I use to enable dynamic virtual hosting on an OSX machine running Apache Web Server.

The result of using this method is that the task of adding a new web server at `http://example.dev` requires only the creation of a folder called `example`.

Apache Virtual Hosting
--
Two things need configuring, the first is _dynamic virtual hosting_ which is controlled via the `httpd-vhosts.conf` file.  This file is commonly found in the `apache/conf/extras` folder (or in `conf/apache/extras` on MAMP). Replace the contents of `httpd-vhosts.conf` with the text below, replacing both instances of `HOSTROOT` with the full path of the folder that will contain root folders of each dynamic host you wish to use.
	
	# Use name-based virtual hosting.
	NameVirtualHost *:80
	
	# Dynamic Virtual Host
	<VirtualHost *:80>
	    ServerAdmin postmaster@example.org
	    ServerName *.example.org
	    UseCanonicalName off
	
	    LogFormat "%V %h %l %u %t \"%r\" %s %b" vcommon
	    ErrorLog "logs/%1.error.log"
	    CustomLog "logs/%1.access.log" vcommon
	
	    # For example:
	    # VirtualDocumentRoot "/Users/yourid/websites/%1"
	    # where within the websites folder you have subfolders called
	    # `example` and `test` which will become the root folders of
	    # `http://example.dev` and `http://test.dev`
	    #
	    VirtualDocumentRoot "/HOSTROOT/%1"
		<Directory "/HOSTROOT/%1" >
		  AllowOverride All
		</Directory>
	
	</VirtualHost>

It is common for this file to be ignored in Apache installs, so you may need to search in your `httpd.conf` file (found in `apache/conf` or `conf/apache/extras` on MAMP) and uncomment this line:

	Include <FULL_PATH_TO_APACHE>/extra/httpd-vhosts.conf

DNS
--
There are several ways to handle domain name resolution.  The simplest (but less automated) is to add example.dev to the /etc/hosts file.  A better way is to run a local DNS server so that a wildcard DNS entry can be created such that anything that matches `*.dev` will resolve to `127.0.0.1` (or `::1` if you're going down the IPv6 route).

I use `dnsmasq` which can be installed via homebrew (`brew install dnsmasq`). Once installed, run the command `cp /usr/local/opt/dnsmasq/dnsmasq.conf.example /usr/local/etc/dnsmasq.conf` which copies the configuration file to `/usr/local/conf`.

Thereafter edit `/usr/local/etc/dnsmasq.conf` and add the line:
	`address=/dev/127.0.0.1`

Run dnsmasq and configure your TCP/IP stack to use this in addition to your other DNS servers.

If all configured correctly, example.dev should resolve to localhost, so visiting http://example.dev should serve the files in your example folder.
  