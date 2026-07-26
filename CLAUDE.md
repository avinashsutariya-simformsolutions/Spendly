# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project status

This is a **learning project**: a Flask expense tracker ("Spendly") being built incrementally, step by step, by a student. Most backend logic is intentionally unimplemented — `app.py` and `database/db.py` contain placeholder routes/functions with comments like `# Students will write this file in Step 1` and `return "Add expense — coming in Step 7"`. When asked to implement a route or feature, implement only what's being asked for that step rather than filling in all the other placeholders at once, unless told otherwise.

## Commands

Run from the repo root. A venv already exists at `venv/`.

```
venv\Scripts\activate          # activate the virtualenv (PowerShell: venv\Scripts\Activate.ps1)
pip install -r requirements.txt
python app.py                  # run the dev server (http://127.0.0.1:5001, debug=True)
pytest                         # run tests
pytest path/to/test_file.py::test_name   # run a single test
```

There is no lint/format tooling configured in this repo.

## Architecture

Standard Flask app, not a package/blueprint structure — everything routes through a single `app.py`:

- **`app.py`** — the Flask app and all routes. Currently a flat list of `@app.route` view functions (no blueprints). Static pages (`landing`, `register`, `login`, `terms`, `privacy`) render templates directly; the rest (`logout`, `profile`, `expenses/add`, `expenses/<id>/edit`, `expenses/<id>/delete`) are stub routes returning placeholder strings, to be implemented in later steps.
- **`database/db.py`** — intended to hold `get_db()` (SQLite connection with `row_factory` and foreign keys enabled), `init_db()` (create tables with `CREATE TABLE IF NOT EXISTS`), and `seed_db()` (sample dev data). Not yet implemented.
- **`templates/`** — Jinja2 templates extending `templates/base.html`, which defines the shared nav/footer shell and the `title`/`head`/`content`/`scripts` blocks. Forms (`register.html`, `login.html`) POST to `/register` and `/login` respectively with `name`/`email`/`password` fields, and render an `{% if error %}` block for auth errors.
- **`static/css/style.css`** and **`static/js/main.js`** — global stylesheet and JS, included by `base.html` on every page.

Auth/session handling, expense CRUD, and the database layer are not yet wired up — routes referencing them are still placeholders per the comments in `app.py`.
