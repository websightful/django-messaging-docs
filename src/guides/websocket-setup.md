# WebSocket Setup

WebSocket transport provides real-time, instant message delivery. This guide will help you set up WebSocket support for Django Messaging.

## Overview

WebSocket setup requires:

1. Django Channels for WebSocket support
2. Redis for channel layer backend
3. An ASGI server (Daphne or Uvicorn)
4. Proper configuration in Django settings

## Prerequisites

- Django Messaging installed (see [Installation](../getting-started/installation.md))
- Redis server (local or remote)
- Python 3.8 or higher

## Step 1: Install Dependencies

Choose one of the following ASGI server options:

### Option A: Using Daphne (Recommended)

```bash
pip install channels channels-redis daphne
```

### Option B: Using Uvicorn

```bash
pip install channels channels-redis uvicorn websockets
```

## Step 2: Update INSTALLED_APPS

Add the required apps to your `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    # Add daphne BEFORE django.contrib apps (only if using Daphne)
    "daphne",
    
    # Django contrib apps
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    
    # Third-party apps
    "rest_framework",
    "django_messaging",
    
    # Your apps
    # ...
]
```

!!! warning
    If using Daphne, it **must** be listed before the Django contrib apps to properly handle ASGI.

## Step 3: Configure ASGI Application

Create or modify your project's `asgi.py` file:

```python
# myproject/asgi.py
import os

from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
from django_messaging.routing import websocket_urlpatterns

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")

application = ProtocolTypeRouter(
    {
        "http": get_asgi_application(),
        "websocket": AuthMiddlewareStack(
            URLRouter(websocket_urlpatterns)
        ),
    }
)
```

## Step 4: Configure Django Settings

Add the following to your `settings.py`:

```python
# ASGI application
ASGI_APPLICATION = "myproject.asgi.application"

# Channel layers configuration
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
        },
    },
}

# Django Messaging configuration
DJANGO_MESSAGING = {
    "TRANSPORT": "websocket",
    # ... other settings
}
```

### Redis Configuration Options

#### Local Redis (Development)

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
        },
    },
}
```

#### Remote Redis (Production)

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("redis.example.com", 6379)],
        },
    },
}
```

#### Redis with Authentication

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [
                {
                    "address": "redis://username:password@redis.example.com:6379/0",
                }
            ],
        },
    },
}
```

#### Redis Sentinel (High Availability)

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [
                {
                    "sentinels": [
                        ("sentinel1.example.com", 26379),
                        ("sentinel2.example.com", 26379),
                        ("sentinel3.example.com", 26379),
                    ],
                    "master_name": "mymaster",
                }
            ],
        },
    },
}
```

## Step 5: Install and Start Redis

### Using Docker (Recommended for Development)

```bash
docker run -p 6379:6379 -d redis:latest
```

### Using Homebrew (macOS)

```bash
# Install Redis
brew install redis

# Start Redis service
brew services start redis

# Or run manually
redis-server
```

### Using apt (Ubuntu/Debian)

```bash
# Install Redis
sudo apt update
sudo apt install redis-server

# Start Redis service
sudo systemctl start redis-server

# Enable Redis to start on boot
sudo systemctl enable redis-server
```

### Verify Redis is Running

```bash
redis-cli ping
```

You should see `PONG` as the response.

## Step 6: Run the Development Server

### Using Daphne

With Daphne installed and in `INSTALLED_APPS`, you can use the standard Django command:

```bash
python manage.py runserver
```

Daphne will automatically handle both HTTP and WebSocket connections.

### Using Uvicorn

```bash
uvicorn myproject.asgi:application --host 127.0.0.1 --port 8000 --reload
```

## Step 7: Test WebSocket Connection

1. Open your browser's developer console (F12)
2. Navigate to `http://127.0.0.1:8000/messages/`
3. Look for WebSocket connection messages in the console
4. You should see something like: `WebSocket connection established`

## Production Deployment

### Using Daphne

For production with Daphne:

```bash
daphne -b 0.0.0.0 -p 8000 myproject.asgi:application
```

#### Systemd Service for Daphne

Create `/etc/systemd/system/daphne.service`:

```ini
[Unit]
Description=Daphne ASGI Server for Django Messaging
After=network.target redis.service

[Service]
Type=simple
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/daphne \
    -b 0.0.0.0 \
    -p 8000 \
    myproject.asgi:application

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable daphne
sudo systemctl start daphne
sudo systemctl status daphne
```

### Using Uvicorn

For production with Uvicorn:

```bash
uvicorn myproject.asgi:application --host 0.0.0.0 --port 8000 --workers 4
```

#### Systemd Service for Uvicorn

Create `/etc/systemd/system/uvicorn.service`:

```ini
[Unit]
Description=Uvicorn ASGI Server for Django Messaging
After=network.target redis.service

[Service]
Type=simple
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/uvicorn \
    myproject.asgi:application \
    --host 0.0.0.0 \
    --port 8000 \
    --workers 4 \
    --log-level info

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable uvicorn
sudo systemctl start uvicorn
sudo systemctl status uvicorn
```

### Using Gunicorn with Uvicorn Workers

```bash
pip install gunicorn uvicorn

gunicorn myproject.asgi:application \
    -k uvicorn.workers.UvicornWorker \
    --bind 0.0.0.0:8000 \
    --workers 4
```

#### Systemd Service for Gunicorn + Uvicorn

Create `/etc/systemd/system/gunicorn.service`:

```ini
[Unit]
Description=Gunicorn ASGI Server with Uvicorn Workers for Django Messaging
After=network.target redis.service

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/gunicorn \
    myproject.asgi:application \
    -k uvicorn.workers.UvicornWorker \
    --bind 0.0.0.0:8000 \
    --workers 4 \
    --log-level info \
    --access-logfile /var/log/gunicorn/access.log \
    --error-logfile /var/log/gunicorn/error.log

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Create log directory:

```bash
sudo mkdir -p /var/log/gunicorn
sudo chown www-data:www-data /var/log/gunicorn
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable gunicorn
sudo systemctl start gunicorn
sudo systemctl status gunicorn
```

### Systemd Service Configuration Notes

**Important settings to customize:**

- `User` and `Group`: Change from `www-data` to your application user
- `WorkingDirectory`: Set to your Django project root directory
- `Environment="PATH=..."`: Set to your virtual environment's bin directory
- `ExecStart`: Update paths to match your installation
- `--workers`: Adjust based on CPU cores (typically 2-4 × CPU cores)

**Useful systemd commands:**

```bash
# View logs
sudo journalctl -u daphne -f
sudo journalctl -u uvicorn -f
sudo journalctl -u gunicorn -f

# Restart service
sudo systemctl restart daphne
sudo systemctl restart uvicorn
sudo systemctl restart gunicorn

# Stop service
sudo systemctl stop daphne

# Check status
sudo systemctl status daphne
```

### Nginx Configuration

Example Nginx configuration for WebSocket support:

```nginx
upstream django {
    server 127.0.0.1:8000;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://django;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static/ {
        alias /path/to/staticfiles/;
    }

    location /media/ {
        alias /path/to/media/;
    }
}
```

## Troubleshooting

### WebSocket Connection Failed

**Problem**: WebSocket connection fails in the browser console.

**Solutions**:

1. Verify Redis is running: `redis-cli ping`
2. Check `ASGI_APPLICATION` setting points to correct ASGI application
3. Ensure `websocket_urlpatterns` is imported correctly
4. Check browser console for specific error messages

### Messages Not Appearing in Real-Time

**Problem**: Messages don't appear instantly.

**Solutions**:

1. Verify `TRANSPORT` is set to `"websocket"` in settings
2. Check WebSocket connection is established (browser console)
3. Verify Redis is running and accessible
4. Check `CHANNEL_LAYERS` configuration

### Redis Connection Error

**Problem**: `Connection refused` or `Cannot connect to Redis` errors.

**Solutions**:

1. Verify Redis is running: `redis-cli ping`
2. Check Redis host and port in `CHANNEL_LAYERS` configuration
3. Verify firewall allows connection to Redis port
4. Check Redis authentication if using password

### Import Error for websocket_urlpatterns

**Problem**: `ImportError: cannot import name 'websocket_urlpatterns'`

**Solutions**:

1. Verify Django Messaging is installed: `pip list | grep django-messaging`
2. Check `django_messaging` is in `INSTALLED_APPS`
3. Restart the development server

## Performance Tuning

### Redis Connection Pool

For better performance, configure Redis connection pooling:

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
            "capacity": 1500,
            "expiry": 10,
        },
    },
}
```

### Channel Layer Capacity

Adjust capacity based on your expected concurrent users:

- Small sites (< 100 concurrent users): `capacity: 1500`
- Medium sites (100-1000 concurrent users): `capacity: 5000`
- Large sites (> 1000 concurrent users): `capacity: 10000`

## Next Steps

- Learn about [Customization](customization.md) options
- Explore [Embedding Widgets](embedding-widgets.md)
- Check the [API Reference](../reference/api.md)
- Review [Settings Reference](../reference/settings.md)
