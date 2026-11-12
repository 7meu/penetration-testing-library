# Bettercap
```sh
$ sudo bettercap -iface wlan0 # can be changed with set wifi.interface wlanX
wlan0  » wifi.recon on
wlan0  » wifi.recon.channel 6,11
# A will give you autofill
# 2x A shows all modules and commands
wlan0  » set ticker.commands "clear; wifi.show"
wlan0  » wifi.recon on
wlan0  » ticker on
```

Equivalent: `sudo bettercap -iface wlan0 -eval "set ticker.commands 'clear; wifi.show'; wifi.recon on; ticker on"`

```sh
wlan0  » set wifi.show.sort clients desc
wlan0  » wifi.show
```

```sh
wlan0  » set wifi.show.filter "WPA2"
wlan0  » wifi.show
```

```sh
wlan0  » wifi.recon c6:2d:56:2a:53:f8
wlan0  » wifi.show
wlan0  » set wifi.show.filter ^c0 # MACs starting with c0
wlan0  » wifi.show
wlan0  » set wifi.show.filter ""  # clear
wlan0  » set wifi.rssi.min -49    # filter by signal strength
wlan0  » wifi.show
wlan0  » wifi.deauth c6:2d:56:2a:53:f8 # BSSID
wlan0  » wifi.deauth ac:22:0b:28:fd:22 # client MAC
wlan1  » wifi.recon off
wlan1  » get wifi.handshakes.file
wlan0  » set wifi.deauth.skip ac:22:0b:28:fd:22 # if wifi.deauth collapses because it's not in deauth skip list
```

```sh
$ ls /usr/share/bettercap/caplets/
$ sudo bettercap -iface wlan0 -caplet deauth_corp.cap # and you wait
```

# Web interface
```sh
$ sudo nft add table inet filter
$ sudo nft 'add chain inet filter INPUT { type filter hook input priority 0; policy drop; }'
$ sudo nft add rule inet filter INPUT ip saddr 192.168.62.192 tcp dport 443 accept
$ sudo nft add rule inet filter INPUT ip saddr 192.168.62.192 tcp dport 8083 accept
```

```sh
$ cat -n /usr/share/bettercap/caplets/https-ui.cap
# !!! CHANGE THESE !!!
set api.rest.username offsec
set api.rest.password wifu
```

```sh
$ sudo bettercap -iface wlan0 -caplet https-ui # or http
```

# Kismet
```sh
$ sudo kismet -c wlan0 --no-ncurses
$ sudo kismet -c wlan0:channels="4,5,6"
$ sudo kismet --daemonize # background
```

# Remote
```sh
$ sudo kismet
$ ssh kali@192.168.62.192 -L 8000:localhost:3501
kali@~ $ sudo kismet_cap_linux_wifi --connect 127.0.0.1:8000 --user offsec --password lab --source=wlan0:name=remote-wlan0
```

`.kismet` files are opened with `sqlite3`

```sh
$ sudo kismet -c Documents/Network_Join_Nokia_Mobile.pcap:realtime=true
$ kismetdb_to_pcap --in Kismet-20200917-18-45-34-1.kismet --list-datasources
$ kismetdb_to_pcap --in Kismet-20200917-18-45-34-1.kismet --out sample.pcapng --verbose
$ kismetdb_dump_devices --in /var/log/kismet/Kismet-20200917-17-45-17-1.kismet --out sample.json --skip-clean --verbose
```
