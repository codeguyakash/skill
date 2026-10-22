---
name: security
description: Secure coding rules (OWASP-aligned) for Node/React/Next/Mongo/Postgres apps. Use when handling auth, user input, secrets, uploads, APIs, dependencies, Docker, or CI.
---

# Security

## Secrets
- Never commit secrets. `.env` in `.gitignore`; commit `.env.example` with placeholders.
- Validate env at boot with a schema; fail fast.
- Rotate any secret that was ever committed (removing from history is not enough).
- CI/CD: use platform secret stores, scoped per environment.

## Input validation
```ts
import { z } from 'zod';
const CreateUser = z.object({
  email: z.string().email().max(254),
  password: z.string().min(12).max(128),
  name: z.string().trim().min(1).max(100),
}).strict();
```
- Validate body, query, params, headers. Reject unknown fields.
- Allow-list, don't block-list. Enforce length and size limits.

## Injection
- SQL: parameterized queries / ORM only. Never string-concatenate.
- Mongo: cast IDs, strip `$` and `.` operators (`express-mongo-sanitize`), never pass `req.body` directly into a query.
- Shell: avoid `exec`; use `execFile` with argument arrays.
- Paths: resolve and verify within an allowed base directory.

## Auth
- Hash with argon2id or bcrypt (cost ≥ 12). Never store plaintext or reversible.
- JWT: pin algorithm, verify `exp`/`iss`/`aud`, access token 15 min, refresh rotation with revocation.
- Cookies: `HttpOnly; Secure; SameSite=Lax|Strict`.
- Authorization check on **every** resource access (ownership/role), server-side. Hiding UI is not security.
- Rate limit and lock out: login, OTP, reset (`express-rate-limit`).
- Generic auth errors ("Invalid email or password"); no user enumeration.

## Web
- XSS: React escapes by default; never `dangerouslySetInnerHTML` with untrusted data (sanitize with DOMPurify if unavoidable).
- CSP, `X-Content-Type-Options`, `Referrer-Policy`, HSTS via `helmet`.
- CORS: explicit origin allow-list, no `*` with credentials.
- CSRF: SameSite cookies + token for cookie-auth state-changing requests.
- Open redirects: allow-list redirect targets.
- Uploads: check MIME by content, limit size, random filenames, store outside web root / object storage, scan if possible.

## Dependencies
- `npm audit --omit=dev` in CI; fail on high/critical.
- Lockfile committed; `npm ci` in CI.
- Prefer few, maintained, well-known packages. Review install scripts.
- Enable Dependabot / Renovate.

## Data
- Encrypt in transit (TLS) and at rest.
- Collect minimum PII. Never log passwords, tokens, card data, or full PII.
- Backups tested and encrypted.

## Docker
```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine
ENV NODE_ENV=production
WORKDIR /app
COPY --from=build --chown=node:node /app/dist ./dist
COPY --from=build --chown=node:node /app/package*.json ./
RUN npm ci --omit=dev && npm cache clean --force
USER node
EXPOSE 3000
HEALTHCHECK CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```
- Non-root user, pinned base image, `.dockerignore` includes `.env`, `.git`, `node_modules`.

## Checklist
- [ ] No secrets in repo or logs
- [ ] All input validated
- [ ] AuthZ on every endpoint
- [ ] Rate limits on sensitive routes
- [ ] Security headers + strict CORS
- [ ] `npm audit` clean (high/critical)
- [ ] Errors don't leak internals
