# Deploy SkyLearn on Render

This repository keeps SQLite for local development. Set `DATABASE_URL` in Render to use PostgreSQL. Local accounts and sample data are not uploaded automatically.

1. Create a Render Postgres database in the same region as the web service. Copy its **Internal Database URL**.
2. Create a Render Web Service from your GitHub fork, branch `main`, using the Python runtime. The repository's `.python-version` selects Python 3.10.11 because its pinned dependencies do not support Render's newer default.
3. Set the build command to `bash build.sh` and the start command to `gunicorn config.wsgi:application`.
4. Set these environment variables in Render, not in Git:

   - `DATABASE_URL`: Postgres Internal Database URL.
   - `SECRET_KEY`: a newly generated, private Django secret.
   - `DEBUG`: `False`.
   - `EMAIL_BACKEND`: `django.core.mail.backends.smtp.EmailBackend`.
   - `EMAIL_HOST`: `smtp.gmail.com`.
   - `EMAIL_PORT`: `587`.
   - `EMAIL_USE_TLS`: `True`.
   - `EMAIL_HOST_USER`: the sending Gmail address.
   - `EMAIL_HOST_PASSWORD`: its Google App Password.
   - `EMAIL_FROM_ADDRESS`: `SkyLearn <the-sending-address@gmail.com>`.

   Render supplies `RENDER` and `RENDER_EXTERNAL_HOSTNAME`; the app uses these for production security settings and its default public URL. Set `SITE_URL=https://your-service.onrender.com` if you want an explicit email link. For a custom domain, set `ALLOWED_HOSTS` to include it and add its HTTPS origin to `CSRF_TRUSTED_ORIGINS` in Django settings before using it.

5. Deploy. `build.sh` installs production requirements, collects static files, and runs migrations. Create a new admin account on the deployed service after the database is ready.

**Limitations:** Render's default filesystem is temporary. PostgreSQL persists application records, but user-uploaded media still requires persistent object storage or a paid persistent disk. The existing Django 4.0.8 dependency is outdated and should be upgraded and retested before serving real users or data.
