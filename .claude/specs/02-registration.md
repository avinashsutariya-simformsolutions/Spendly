# Spec: Registration

## Overview

Implement account creation so a visitor can sign up for Spendly with a name, email, and password. This is the second step of the roadmap, building directly on the `users` table and `database/db.py` helpers delivered in Step 1. Registration is the entry point for every other feature — login, profile, and expense tracking all require a real user row to exist — so this step focuses narrowly on turning the existing `/register` page into a working POST handler that validates input, prevents duplicate emails, hashes passwords, and creates the user.

## Depends on

- Step 01 — Database setup (`database/db.py`: `get_db()`, `init_db()`, the `users` table). Registration reads/writes the `users` table via `get_db()`.

## Routes

- GET /register — render the registration form — public (already implemented, unchanged)
- POST /register — validate submitted name/email/password, reject duplicate emails and invalid input, hash the password, insert the new user, then redirect to `/login` — public

## Database changes

No database changes. Uses the existing `users` table (`id`, `name`, `email`, `password_hash`, `created_at`) exactly as created in `database/db.py`.

## Templates

- Create: none
- Modify: `templates/register.html` — no structural changes needed; it already posts to `/register` with `name`/`email`/`password` fields and renders `{% if error %}`. Re-populate the `name`/`email` field values on validation failure so the user doesn't have to retype them (add `value="{{ name or '' }}"` / `value="{{ email or '' }}"` to those two inputs).

## Files to change

- `app.py` — change the `/register` route to accept `GET` and `POST`; on `POST`, validate input, check for an existing email, hash the password with `werkzeug.security.generate_password_hash`, insert the user via a parameterized query, and redirect to `/login` on success or re-render `register.html` with an `error` (and the submitted `name`/`email`) on failure.
- `templates/register.html` — re-populate `name`/`email` values as described above.

## Files to create

- None

## New dependencies

No new dependencies.

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (`generate_password_hash`)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Only implement registration in this step — do not implement login/session handling, even though `register.html` links to `/login`
- Use `get_db()` from `database/db.py` for all queries; open and close the connection per request

## Definition of done

- [ ] Visiting `/register` in a browser still shows the existing registration form
- [ ] Submitting the form with a new name/email/password creates a row in the `users` table with a hashed password (verify it is not stored in plain text)
- [ ] Submitting with an email that already exists (e.g. `demo@spendly.com`) re-renders the form with an error message and does not create a duplicate row
- [ ] Submitting with a missing name, email, or password re-renders the form with an error message instead of crashing
- [ ] On successful registration, the browser is redirected to `/login`
- [ ] Re-running the app and registering another user does not affect the existing demo user or expenses
