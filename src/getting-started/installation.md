# Installation

This guide will walk you through installing Django Messaging in your Django project.

## Prerequisites

Before installing Django Messaging, ensure you have:

- Python 3.8 or higher
- Django 4.2 or higher
- A Django project set up and running

## Step 1: Purchase and Download

Get the `django_messaging-0.21.1-py3-none-any.whl` package from [Gumroad](https://websightful.gumroad.com/l/django-messaging).

## Step 2: Install the Package

There are two ways to install the package:

### Option A: Using requirements.txt (Recommended)

1. Create a directory `private_wheels/` in your project's repository:

```bash
mkdir private_wheels
```

2. Copy the wheel file to this directory:

```bash
cp /path/to/django_messaging-0.21.1-py3-none-any.whl private_wheels/
```

3. Add the package to your `requirements.txt`:

```text
Django>=4.2
djangorestframework>=3.14
file:./private_wheels/django_messaging-0.21.1-py3-none-any.whl
```

4. Install the requirements:

```bash
pip install -r requirements.txt
```

### Option B: Direct Installation

Install the wheel file directly:

```bash
pip install /path/to/django_messaging-0.21.1-py3-none-any.whl
```

## Step 3: Add to INSTALLED_APPS

Add `django_messaging` to your `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    # For WebSocket support (optional, add before django.contrib apps)
    "daphne",  # Only if using WebSocket transport

    # Django contrib apps
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Third-party apps
    "rest_framework",  # Required
    "django_messaging",  # Add this

    # Your apps
    # ...
]
```

!!! note
    If you plan to use WebSocket transport, add `"daphne"` before the Django contrib apps. For polling transport, you can skip this.

## Step 4: Add Context Processor

Add the context processor to your `TEMPLATES` configuration in `settings.py`:

```python
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.debug",
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
                "django_messaging.context_processors.messaging_settings",  # Add this
            ],
        },
    },
]
```

## Step 5: Configure URLs

Include the app URLs in your project's `urls.py`:

```python
from django.urls import path, include

urlpatterns = [
    # ... your other URL patterns
    path("messages/", include("django_messaging.urls")),
]
```

!!! tip
    You can use any URL prefix you prefer. For example, `path("chat/", include("django_messaging.urls"))` would make the messaging interface available at `/chat/`.

## Step 6: Run Migrations

Apply the database migrations:

```bash
python manage.py migrate
```

## Step 7: Collect Static Files

Collect the static files for production:

```bash
python manage.py collectstatic
```

## Verify Installation

To verify that Django Messaging is installed correctly:

1. Start your development server:

```bash
python manage.py runserver
```

2. Navigate to `http://127.0.0.1:8000/messages/` in your browser

3. You should see the messaging interface (you'll need to be logged in)

## Next Steps

Now that Django Messaging is installed, you can:

- Follow the [Quick Start Guide](quick-start.md) to set up your first chat
- Configure [WebSocket support](../guides/websocket-setup.md) for real-time messaging
- Or use [Polling transport](../guides/polling-setup.md) for a simpler setup
- Customize the app with [Configuration options](configuration.md)

## Troubleshooting

### Import Error

If you get an import error, make sure:

- The package is installed in your virtual environment
- `django_messaging` is in your `INSTALLED_APPS`
- You've activated your virtual environment

### Static Files Not Loading

If static files aren't loading:

- Run `python manage.py collectstatic`
- Check that `django.contrib.staticfiles` is in `INSTALLED_APPS`
- Verify your `STATIC_URL` and `STATIC_ROOT` settings

### Database Errors

If you get database errors:

- Make sure you've run `python manage.py migrate`
- Check that your database is properly configured in `settings.py`

