# Drupal Discovery and Enumeration

## 1. Basic Identification

```bash
curl -s http://drupal.inlanefreight.local | grep Drupal
curl -s http://drupal.inlanefreight.local/robots.txt
curl -s http://drupal.inlanefreight.local/node/1
curl -s http://drupal.inlanefreight.local/CHANGELOG.txt
```

## 2. Version Enumeration

```bash
droopescan scan drupal -u http://drupal.inlanefreight.local
curl -s http://drupal.inlanefreight.local/core/assets/vendor/jquery/jquery.min.js | grep "Drupal"
curl -s http://drupal.inlanefreight.local/rss.xml | grep generator
```

**Database check (if access gained):**

```sql
SELECT version FROM system;
```

## 3. Module Enumeration

```bash
curl -s http://drupal.inlanefreight.local/modules/
curl -s http://drupal.inlanefreight.local/modules/[module_name]/[module_file].info.yml
```

## 4. Theme Enumeration

```bash
curl -s http://drupal.inlanefreight.local/themes/
curl -s http://drupal.inlanefreight.local/themes/[theme_name]/style.css
curl -s http://drupal.inlanefreight.local/themes/[theme_name]/[theme_file].info.yml
```

## 5. Configuration Files

```bash
curl -s http://drupal.inlanefreight.local/sites/default/settings.php
```

## 6. User Enumeration

```bash
curl -s http://drupal.inlanefreight.local/?q=user
curl -s http://drupal.inlanefreight.local/user/1
```

## 7. REST API Enumeration

```bash
curl -s http://drupal.inlanefreight.local/rest/user/login_status?_format=json
curl -s http://drupal.inlanefreight.local/rest/
curl -s http://drupal.inlanefreight.local/rest/export?_format=json
```

## 8. Content Enumeration

```bash
curl -s http://drupal.inlanefreight.local/node/2
curl -s http://drupal.inlanefreight.local/node.json
```

## 9. Backup & Debug Files

```bash
curl -s http://drupal.inlanefreight.local/sites/default/files/backup.sql
curl -s http://drupal.inlanefreight.local/phpinfo.php
```

## 10. Admin Panel Access

```bash
curl -s http://drupal.inlanefreight.local/user/login
curl -s http://drupal.inlanefreight.local/admin
```

## 11. GraphQL API Enumeration

```bash
curl -X POST http://drupal.inlanefreight.local/graphql -H "Content-Type: application/json" --data '{"query":"{__schema { types { name } }}"}'
```

## 12. Robots.txt Analysis

```bash
curl -s http://drupal.inlanefreight.local/robots.txt
```
