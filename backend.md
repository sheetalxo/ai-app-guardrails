# Backend reference

## Principles
- Thin routes, logic in services, data access in one layer. One responsibility per file; no 2,000-line files.
- Config from environment; fail fast at startup if a required variable is missing.
- Every endpoint: authenticate → authorize → validate → act → respond with consistent JSON `{ ok, data | error }`.
- Correct status codes (400 validation, 401 not logged in, 403 forbidden, 404 missing, 409 conflict, 429 rate limited, 500 server).
- Idempotent writes for payments/orders (idempotency key) so a double-click cannot charge twice.
- Use DB transactions when several writes must succeed together.

## Database
- Define primary keys, foreign keys, `NOT NULL`, `UNIQUE` and indexes on lookup columns. Constraints catch bugs the code misses.
- Store money as integer minor units (paise/cents), timestamps in UTC.
- Never `SELECT *` into API responses — return only the fields the client needs (never password hashes).
- Migrations/`schema.sql` included so setup is reproducible.

## Node/Express skeleton (pattern)
```js
import express from 'express';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import { z } from 'zod';

const app = express();
app.use(helmet());
app.use(express.json({ limit: '100kb' }));

const authLimiter = rateLimit({ windowMs: 15*60*1000, limit: 10 });

const LoginSchema = z.object({
  email: z.string().email().max(254),
  password: z.string().min(8).max(128),
});

app.post('/api/login', authLimiter, async (req, res) => {
  const parsed = LoginSchema.safeParse(req.body);
  if (!parsed.success) return res.status(400).json({ ok:false, error:'Invalid input' });
  // look up user with parameterized query, verify hash, regenerate session
  // same generic error for unknown user and wrong password
});

// central error handler: log details server-side, generic message to client
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ ok:false, error:'Something went wrong' });
});
```

## Tell the user (always, at the end)
Env/config values to set, which folders must not be public, how to back up, and how to rotate any key that was ever exposed.
