# Base44 Dev Environment

## Overview
Django to-do app (`example_app`) with SQLite, served by Django's auto-reloading dev server (`runserver`). No external services or secrets required.

## Running
```bash
docker compose -f docker-compose.base44.yml up -d
```
- Web entry point: host port 3000 → container 8000
- Health check: `curl -sf http://localhost:3000/todos/`
- The container installs deps, runs migrations, creates the `admin/admin` superuser, then starts `runserver` with the StatReloader (auto-reloads on file edits).

## Key details
- Source is bind-mounted from `./app` to `/code` inside the container — edits appear live.
- `ALLOWED_HOSTS = ['*']` already accepts the preview's external hostname.
- `CSRF_TRUSTED_ORIGINS` was extended in debug mode to include the preview origin derived from `BASE44_PUBLIC_HOST_SUFFIX` (passed via compose `environment`), so POST actions (add/toggle/delete todo) work from the preview.
- The SQLite DB (`app/db.sqlite3`) and collected static files (`app/staticfiles/`) live on the host via the bind mount and persist across container restarts.
- Admin interface at `/admin/` with username `admin`, password `admin` (created by the `createsuperauto` management command on startup).

## No secrets
This app needs no external credentials. The `SECRET_KEY` is hardcoded in `settings.py` and the database is local SQLite.
