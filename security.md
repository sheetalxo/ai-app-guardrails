# Security reference

Read the sections relevant to the app. Each rule has its reason so you can apply it to cases not listed.

## Secrets & config
- Secrets live in environment variables or a server-only config file outside the web root. Reason: the web root and the browser bundle are public.
- Provide `.env.example` with fake values; add `.env` to `.gitignore`.
- Frontend bundlers expose anything prefixed `VITE_`, `NEXT_PUBLIC_`, `REACT_APP_` — never put keys there.
- Calling Gemini/OpenAI/Stripe/etc. from the browser leaks the key. Route through your own server endpoint that adds the key, authenticates the user, rate-limits and caps usage.

## Authentication & sessions
- Use a proven library/framework feature (NextAuth/Auth.js, Passport, Lucia, Laravel/PHP `password_verify` + sessions). Do not invent token schemes.
- Hash: argon2id or bcrypt (cost ≥ 12). PHP: `password_hash($pw, PASSWORD_DEFAULT)` / `password_verify`.
- Cookies: `HttpOnly; Secure; SameSite=Lax` (or Strict). Regenerate session ID on login (`session_regenerate_id(true)`). Expire idle sessions; logout destroys server session.
- Login errors identical for "no such user" and "wrong password". Add lockout/backoff after repeated failures.
- Password reset: single-use, random (≥128-bit), expiring (≤1h) token; store only its hash; do not reveal whether an email exists.
- Prefer a JWT only when needed; if used: short expiry, verify signature and algorithm explicitly, never store in localStorage if a cookie works.

## Authorization (the most-missed bug)
- Check on every request: *is this user allowed to do this action on this object?* Fetch records scoped by owner (`WHERE id=? AND user_id=?`), never by id alone — otherwise changing `/orders/12` to `/orders/13` exposes someone else's data (IDOR).
- Roles are stored server-side. Never trust a role/isAdmin/price/userId field sent from the client.
- Admin routes need their own server-side check, not just a hidden menu.
- Default deny: new routes are protected unless deliberately made public.

## Input validation & injection
- Validate with a schema (zod/valibot/joi; PHP: `filter_var`, explicit length/type checks). Reject unknown fields. Set max lengths and max body size.
- SQL: prepared statements/ORM only. PHP PDO: `$pdo->prepare('... WHERE id = ?')`, `PDO::ATTR_EMULATE_PREPARES=false`, exceptions on.
- XSS: framework auto-escaping on; never `innerHTML` / `dangerouslySetInnerHTML` / `echo $_GET[...]`. PHP: `htmlspecialchars($s, ENT_QUOTES, 'UTF-8')`. Rich text → DOMPurify (server-side sanitization for stored HTML).
- Command injection: do not shell out with user input. If unavoidable, use argument arrays and allowlists.
- Path traversal: never join user input into file paths; map ids to stored filenames; use `basename`/allowlists.
- SSRF: if the server fetches user-supplied URLs, allowlist hosts and block private/internal IP ranges.
- Open redirects: redirect only to allowlisted internal paths.
- Mass assignment: pick allowed fields explicitly instead of spreading the request body into a DB update.

## CSRF, CORS, headers
- Cookie-based auth requires CSRF protection (token or SameSite + origin check) on every state-changing request. State changes never use GET.
- CORS: explicit origin allowlist. Never `*` together with credentials.
- Set headers: `Content-Security-Policy` (start strict, no inline scripts if possible), `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy`, `Strict-Transport-Security`, `X-Frame-Options: DENY` (or CSP frame-ancestors). Node: `helmet`. Apache: `.htaccess` `Header set`.
- Force HTTPS.

## File uploads
- Allowlist extensions AND check MIME by content (`finfo`), enforce max size, re-name to a random id, never trust the client filename.
- Store outside the web root, or in a directory where script execution is disabled. Serve through a handler that sets `Content-Type` and `Content-Disposition`.
- Images: re-encode (GD/sharp) to strip embedded payloads and EXIF.
- No SVG/HTML uploads from untrusted users unless sanitized (script risk).

## Abuse & availability
- Rate-limit by IP and by account on: login, signup, reset, OTP, contact/inquiry forms, search, uploads, any LLM/payment/SMS call.
- Add a honeypot field or CAPTCHA to public forms.
- Cap pagination (`limit ≤ 100`), request size and query complexity.
- Payments: compute price on the server from your own catalog; verify webhooks by signature; never trust client totals.

## Apps that use an LLM (prompt injection & cost)
- Treat user input, uploaded documents, web content and model output as untrusted data.
- Never execute model output as code/SQL/shell, and never render it as raw HTML. Parse into a strict schema and validate.
- Keep secrets and other users' data out of the prompt; the system prompt can be extracted, so it must hold nothing sensitive.
- Give the model the least tool permissions; require user confirmation for destructive actions.
- Cap tokens, per-user quota and rate; log usage so a stolen endpoint cannot run up the bill.

## Data protection
- Collect only what you need. Encrypt sensitive fields at rest if required; use HTTPS everywhere.
- Never log passwords, tokens, card numbers or full personal data.
- Provide data export/delete for users where relevant. Back up the database and test the restore.

## Dependencies & deployment
- Few, popular, maintained packages; pin versions; run `npm audit` / `composer audit`.
- Disable debug mode and directory listing in production; remove sample/test routes.
- Keep `.git`, `.env`, backups, `composer.json`, DB files and logs unreachable from the web.
