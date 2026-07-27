# Spec: Login and Logout

## Overview

Implement authentication so a registered user can sign in and out of Spendly. This is the third step of the roadmap, building directly on the `users` table from Step 01 and the working registration flow from Step 02. This step turns the existing `/login` page into a working POST handler that verifies email/password against the `users` table and starts a Flask session, adds a `/logout` route that ends the session, and updates the shared nav so it reflects whether a visitor is signed in. It does not implement the profile page or any expense features — those remain placeholders for later steps.

## Depends on

- Step 01 — Database setup (`database/db.py`: `get_db()`, `init_db()`, the `users` table).
- Step 02 — Registration (users exist in the `users` table with a hashed `password_hash`).

## Routes

- GET /login — render the login form — public (already implemented, unchanged)
- POST /login — validate submitted email/password against the `users` table, verify the password hash, store the user's id in the session, redirect to `/profile` on success, or re-render `login.html` with an error on failure — public
- GET /logout — clear the session and redirect to the landing page — logged-in

## Database changes

No database changes. Uses the existing `users` table (`id`, `name`, `email`, `password_hash`, `created_at`) exactly as created in `database/db.py`.

## Templates

- **Create:** none
- **Modify:**
  - `templates/login.html` — re-populate the `email` field value on a failed login attempt (add `value="{{ email or '' }}"` to that input) so the user doesn't have to retype it. Do not re-populate the password field.
  - `templates/base.html` — the nav currently always shows "Sign in" / "Get started". Wrap those links in `{% if session.user_id %}...{% else %}...{% endif %}` so a signed-in visitor instead sees a link to `/profile` and a link to `/logout`.

## Files to change

- `app.py` —
  - Set `app.secret_key` (required for Flask sessions to sign the session cookie).
  - Change `/login` to accept `GET` and `POST`. On `POST`: look up the user by email, verify the password with `werkzeug.security.check_password_hash`, and on success store `session["user_id"] = user["id"]` then redirect to `/profile`. On failure (unknown email or wrong password), re-render `login.html` with a single generic error (e.g. "Invalid email or password.") that doesn't reveal whether the email exists, plus the submitted `email`.
  - Change `/logout` from its placeholder string to clear the session (`session.clear()`) and redirect to `landing`.
- `templates/login.html` — re-populate the `email` value as described above.
- `templates/base.html` — conditional nav links as described above.

## Files to create

- None

## New dependencies

No new dependencies. `check_password_hash` comes from `werkzeug.security`, already a project dependency (used for `generate_password_hash` in registration).

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (`check_password_hash` against the stored `password_hash`)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Only implement login/logout in this step — do not implement the `/profile` page contents or any expense routes, even though `/login` now redirects to `/profile`
- Use `get_db()` from `database/db.py` for all queries; open and close the connection per request
- Use Flask's built-in `session` for auth state — no custom cookies or tokens

## Definition of done

- [ ] Visiting `/login` in a browser still shows the existing login form
- [ ] Logging in with the seeded demo user (`demo@spendly.com` / `demo123`) redirects to `/profile` and sets a session cookie
- [ ] Logging in with a correct email but wrong password re-renders the form with an error and does not set a session
- [ ] Logging in with an email that doesn't exist re-renders the form with the same generic error (doesn't reveal the email is unregistered)
- [ ] After a successful login, the nav shows a link to `/profile` and a "Logout" link instead of "Sign in" / "Get started"
- [ ] Visiting `/logout` while logged in clears the session and redirects to the landing page, and the nav reverts to showing "Sign in" / "Get started"
- [ ] Restarting the app does not affect the demo user or seeded expenses
