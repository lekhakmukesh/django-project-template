# Django Project Template

A Django 6.1 starter with environment-based settings, static/media configuration, and production security settings already in place.

## Requirements

- Python 3.14
- pip

## Setup

```bash
# 1. Clone and enter the project
git clone https://github.com/lekhakmukesh/django-project-template.git my-project
cd my-project

# 2. Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Create your .env file
cp .env.example .env
# Then set a new SECRET_KEY in .env:
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"

# 5. Set up the database and run
cd core
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Open http://localhost:8000/ for the site and http://localhost:8000/admin/ for the admin.

## Environment variables

All configuration lives in `.env` (never committed). See `.env.example` for the full list.

| Variable | Example | Description |
|---|---|---|
| `SECRET_KEY` | *(random string)* | Django secret key. Generate a new one per project. |
| `DEBUG` | `True` | `True` for development, `False` in production. |
| `ALLOWED_HOSTS` | `localhost,127.0.0.1` | Comma-separated hostnames the site can be served on. |
| `EMAIL_BACKEND` | `django.core.mail.backends.console.EmailBackend` | Email backend. Use an SMTP backend in production. |
| `CSRF_TRUSTED_ORIGINS` | `https://example.com` | Production only. Comma-separated origins including `https://`. |
| `SECURE_SSL_REDIRECT` | `True` | Production only. Redirect HTTP to HTTPS. |
| `SECURE_HSTS_SECONDS` | `0` | Production only. Start with `3600` once HTTPS works. |

## Project structure

```
.
├── .env.example          # Template for .env
├── requirements.txt
└── core/                 # Django project root (run manage.py from here)
    ├── manage.py
    ├── core/             # Settings, root URLs, WSGI/ASGI
    ├── api/              # API app (routes under /api/)
    ├── frontend/         # Frontend app (routes under /)
    ├── templates/        # Project-wide templates
    ├── static/           # Project-wide static files (CSS, JS, images)
    ├── staticfiles/      # collectstatic output (git-ignored)
    └── media/            # User uploads (git-ignored)
```

## Adding a new app

```bash
cd core
python manage.py startapp blog
```

Then:

1. Add `'blog'` to `FEATURE_APPS` in `core/core/settings.py`.
2. Create `blog/urls.py` with a `urlpatterns` list.
3. Include it in `core/core/urls.py`: `path('blog/', include('blog.urls'))`.

Settings also has separate lists for `DEBUG_ONLY_APPS`, `CUSTOM_MIDDLEWARE`, `LIBRARY_BASED_MIDDLEWARE` and `DEBUG_ONLY_MIDDLEWARE`.

## Deploying to production

1. In the server's `.env`, set:
   - `DEBUG=False`
   - `SECRET_KEY` to a new random value
   - `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGINS` to your domain
   - `EMAIL_BACKEND` to an SMTP backend
2. Collect static files:
   ```bash
   python manage.py collectstatic
   ```
3. Run Django's deployment checks and fix anything they report:
   ```bash
   python manage.py check --deploy
   ```
4. Serve the app with a production server such as Gunicorn, behind nginx or a similar reverse proxy. The proxy should serve `staticfiles/` at `/static/` and `media/` at `/media/`, and set the `X-Forwarded-Proto` header.

The database is SQLite by default. For production, switch `DATABASES` in `settings.py` to PostgreSQL or another server database.

## Starting a new project from this template

On GitHub, click **Use this template** → **Create a new repository**, then follow [Setup](#setup) with the new repository's URL.
