## OPN
```
network={
    ssid="NAME"
    scan_ssid=1
}
```

## WEP
```
network={
    ssid="NAME"
    key_mgmt=NONE
    wep_key0=0102030405      # aircrack hex no columns (or "password" in quotes)
    wep_tx_keyidx=0
}
```

## WPA/WPA2-PSK
```
network={
    ssid="NAME"
    scan_ssid=1
    psk="PASSWORD"
    key_mgmt=WPA-PSK
    #pairwise=CCMP           # or TKIP if you must force
}
```

Alternative:
```sh
wpa_passphrase NAME PASS > NAME.conf
```

## WPA2-Enterprise (MGT)
```
network={
    ssid="NAME"
    key_mgmt=WPA-EAP
    eap=PEAP
    identity="DOMAIN\user"
    password="PASS"
    phase1="peaplabel=0"
    phase2="auth=MSCHAPV2"
}
```

## Connect
```sh
$ sudo wpa_supplicant -i wlan0 -c NAME.conf        # watch for CTRL-EVENT-CONNECTED
$ sudo wpa_supplicant -i wlan0 -c NAME.conf -B     # background
$ sudo dhclient wlan0
$ ip a
```

## Troubleshooting
- Hidden SSID: `scan_ssid=1`
- WPA negotiates wrong cipher: force `pairwise=CCMP` / `pairwise=TKIP`
- (MGT) `EAP-FAILURE` right after the cert: try `anonymous_identity="anonymous@realm"`, `DOMAIN\\user` or `phase1="peaplabel=0"`
