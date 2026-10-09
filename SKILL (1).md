---
name: secure-app-builder
description: Guardrails for building web apps and websites that are secure, clean and well-designed from the first draft. Use this skill whenever the user asks to build, generate, fix, extend or review ANY app, website, dashboard, API, backend, login system, admin panel, form, e-commerce or PHP/Node/React project — even if they never mention security or design. Covers vulnerability prevention (secrets, auth, injection, XSS, uploads, rate limits), solid backend structure, and clean professional UI/UX. Make sure to use it for every coding task that produces user-facing software.
---

# Secure App Builder

AI-generated apps fail in very predictable ways: secrets in frontend code, "login" that only hides a button, unvalidated input, string-built SQL, placeholder features that look finished but aren't, and generic cluttered UI. This skill exists so none of those ship. Treat every app as if strangers on the internet will attack it on day one, and as if a real customer will judge it in five seconds.

## Workflow

1. **Frame it (before any code).** In 5–8 lines state: what the app does, who the users are, which roles exist, what data is sensitive (passwords, payments, personal data, API keys), and the stack. If something is unclear, pick a safe sensible default and list it as an assumption. Do not interrogate the user.
2. **Threat-think each feature.** For every endpoint, form and action ask: *Who can call this? With what input? What is the worst they can do?* Design the answer in (auth, validation, limits) rather than bolting it on.
3. **Architecture before code.** Give the file structure, data model, auth/roles model, then implement. Read the references as needed:
   - `references/security.md` — always read for anything with users, data, uploads, payments or an LLM.
   - `references/backend.md` — whenever there is a server, database or API.
   - `references/php-shared-hosting.md` — only when the stack is PHP on shared hosting.
   - `references/ui-ux.md` — whenever there is any visible interface.
4. **Build completely.** Real implementations, no fake logins, no mock data presented as real, no "TODO add auth later". If something cannot be done in the environment, say so plainly and state the fix.
5. **Review pass before delivering.** Run the checklist below against your own code. Fix what fails. Then end with a short **Security & quality notes** section: what is protected, and what the user must still do (set env vars, rotate keys, enable HTTPS, back up).

## Non-negotiables (and why)

- **No secrets in client code.** Anything shipped to the browser is public. API keys (including Gemini/OpenAI), DB passwords and tokens live only on a server, read from environment variables. A purely client-side app cannot hide a key — in that case add a thin server/proxy route and say so.
- **The server is the authority.** Hiding a button is not access control. Every request re-checks who the user is and whether they may do *that* action on *that* record.
- **Validate on the server with an allowlist.** Check type, length, format and range of every input; reject the rest. Client validation is only for convenience.
- **Parameterized queries only.** Never build SQL by concatenation or template strings.
- **Escape on output.** No `innerHTML`/`dangerouslySetInnerHTML`/unescaped `echo` with user data. If rich text is needed, sanitize with a vetted library.
- **Passwords:** hash with argon2id or bcrypt (`password_hash` in PHP). Never store plaintext, never invent your own crypto, base64 is not encryption.
- **Rate-limit** login, signup, password reset, contact forms and anything that costs money (LLM calls, SMS, email).
- **Safe errors.** Users get a generic message; details go to server logs. Never show stack traces, SQL errors or file paths.
- **Minimal, well-known dependencies**, pinned versions. Never invent a package or API; if unsure it exists, say so or write it by hand.
- **No hardcoded admin accounts or default passwords.** First admin is created via a setup step or env var, and must change password.

## Common AI mistakes — actively avoid

- Placeholder/mock code that looks real; half-wired features; unused imports; buttons that do nothing.
- Hallucinated functions, packages or config options. Use only what you are sure exists.
- Breaking working code while "improving" it. When editing, change only what was asked, keep everything else identical, and explicitly flag any side effect you could not avoid. Deliver complete replacement files (not fragments) when modifying existing files so the user can paste them directly.
- Trusting the model's own output: if the app uses an LLM, treat user text *and* model output as untrusted (see security reference).
- Inconsistent UI: five different button styles, random spacing, walls of text, no empty/loading/error states.

## Definition of done (self-check)

Security
- [ ] No secret, key or token appears in any frontend file or repo
- [ ] Every protected route/endpoint checks authentication AND authorization (including per-record ownership)
- [ ] All input validated server-side; all queries parameterized; all output escaped
- [ ] Passwords hashed; sessions/cookies `HttpOnly`, `Secure`, `SameSite`; CSRF handled for cookie auth
- [ ] Uploads: type/size checked, renamed, stored outside web root or non-executable
- [ ] Rate limiting on auth and costly endpoints; CORS is an allowlist, not `*`
- [ ] Errors generic to users; security headers set
Quality
- [ ] Runs end-to-end with no placeholder features; edge cases (empty, invalid, duplicate, offline) handled
- [ ] Loading, empty, error and success states exist for every data view and form
- [ ] Responsive from 360px up; keyboard-usable; contrast ≥ 4.5:1
- [ ] Consistent design tokens (colors, spacing, type, radius) — no one-off values
- [ ] `.env.example` provided; README lists setup and the user's remaining to-dos

## Output format

1. Assumptions (short list)
2. File structure
3. Code (complete files, each with its path as a heading)
4. Security & quality notes (protected / still your job)
