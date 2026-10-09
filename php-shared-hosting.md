# PHP on shared hosting reference

Use only when the project is PHP.

Shared hosting has no VPS controls, so rely on code discipline:
- **Data outside the web root.** Keep SQLite files, JSON data, logs and config in a folder *above* `public_html` when possible. If not possible, add `.htaccess` in that folder: `Require all denied` (Apache 2.4) or `Deny from all`, and name files unguessably. Test by opening the URL in a browser.
- **Config:** `config.php` outside web root (or protected), never committed; no secrets in JS.
- **PDO always:**
```php
$pdo = new PDO('sqlite:'.__DIR__.'/../data/app.db', null, null, [
  PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
  PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
  PDO::ATTR_EMULATE_PREPARES => false,
]);
$stmt = $pdo->prepare('SELECT id,name FROM users WHERE email = ?');
$stmt->execute([$email]);
```
- **Flat-file JSON:** read-modify-write under `flock(LOCK_EX)` to prevent corruption from concurrent requests; validate/escape on read as well as write; keep files small, otherwise move to SQLite.
- **Sessions:** `session_set_cookie_params(['httponly'=>true,'secure'=>true,'samesite'=>'Lax'])`, `session_regenerate_id(true)` after login, CSRF token in every POST form (`hash_equals` to compare).
- **Output:** `htmlspecialchars($v, ENT_QUOTES, 'UTF-8')` on every echoed variable.
- **Production:** `display_errors=0`, `log_errors=1`, log file outside web root. Disable directory listing (`Options -Indexes`).
- **Uploads:** see security reference; never store in a folder that executes `.php`; add `php_flag engine off` or `.htaccess` deny for scripts where supported.
- **Rate limiting without Redis:** count attempts per IP+account in the DB/file with timestamps; block after N tries per window.
- **Mail/contact forms:** validate email, strip newlines from header fields (header injection), rate-limit, honeypot.

