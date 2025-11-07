# Polling Setup

Polling transport provides a simpler alternative to WebSocket for message delivery. This guide will help you set up polling transport for Django Messaging.

## Overview

Polling transport:

- ✅ **Simple setup** - No Redis or Django Channels required
- ✅ **Works with standard Django** - Uses regular WSGI servers
- ✅ **Easy deployment** - No special server configuration needed
- ⚠️ **Slight delay** - Messages appear with a small delay (configurable)
- ⚠️ **More server requests** - Clients poll the server periodically

## When to Use Polling

Polling is ideal for:

- **Development and testing** - Quick setup without additional dependencies
- **Small to medium sites** - Where real-time delivery isn't critical
- **Shared hosting** - Where WebSocket support isn't available
- **Simple deployments** - Where you want to avoid Redis setup

Consider WebSocket if you need:

- Instant message delivery
- High-traffic sites with many concurrent users
- Real-time typing indicators and presence

## Step 1: Configure Polling Transport

Add this to your `settings.py`:

```python
DJANGO_MESSAGING = {
    "TRANSPORT": "polling",
}
```

That's it! No additional dependencies or configuration required.

## Step 2: Run the Development Server

Use the standard Django development server:

```bash
python manage.py runserver
```

## How Polling Works

With polling transport:

1. The client sends periodic requests to the server (every 3 seconds by default)
2. The server checks for new messages and returns them
3. The client updates the UI with any new messages
4. The process repeats

This creates a near-real-time experience with minimal setup.

## Customizing Polling Behavior

While the polling interval is not directly configurable through settings, you can customize it by overriding the JavaScript if needed.

### Default Behavior

- **Polling interval**: 3 seconds
- **Timeout**: 30 seconds per request
- **Retry on error**: Automatic with exponential backoff

## Production Deployment

Polling works with any standard WSGI server:

### Using Gunicorn

```bash
pip install gunicorn

gunicorn myproject.wsgi:application --bind 0.0.0.0:8000 --workers 4
```

#### Systemd Service for Gunicorn

Create `/etc/systemd/system/gunicorn.service`:

```ini
[Unit]
Description=Gunicorn WSGI Server for Django Messaging
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/gunicorn \
    myproject.wsgi:application \
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

Create log directory and enable the service:

```bash
sudo mkdir -p /var/log/gunicorn
sudo chown www-data:www-data /var/log/gunicorn
sudo systemctl daemon-reload
sudo systemctl enable gunicorn
sudo systemctl start gunicorn
sudo systemctl status gunicorn
```

### Using uWSGI

```bash
pip install uwsgi

uwsgi --http :8000 --module myproject.wsgi:application --workers 4
```

#### Systemd Service for uWSGI

Create `/etc/systemd/system/uwsgi.service`:

```ini
[Unit]
Description=uWSGI Server for Django Messaging
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/uwsgi \
    --http :8000 \
    --module myproject.wsgi:application \
    --workers 4 \
    --master \
    --enable-threads \
    --logto /var/log/uwsgi/uwsgi.log

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Create log directory and enable the service:

```bash
sudo mkdir -p /var/log/uwsgi
sudo chown www-data:www-data /var/log/uwsgi
sudo systemctl daemon-reload
sudo systemctl enable uwsgi
sudo systemctl start uwsgi
sudo systemctl status uwsgi
```

### Using mod_wsgi (Apache)

Configure Apache with mod_wsgi as you normally would for Django applications.

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
sudo journalctl -u gunicorn -f
sudo journalctl -u uwsgi -f

# Restart service
sudo systemctl restart gunicorn
sudo systemctl restart uwsgi

# Stop service
sudo systemctl stop gunicorn

# Check status
sudo systemctl status gunicorn
```

## Nginx Configuration

Example Nginx configuration for polling transport:

```nginx
upstream django {
    server 127.0.0.1:8000;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://django;
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

## Performance Considerations

### Server Load

Polling creates more HTTP requests than WebSocket. For a site with 100 concurrent users:

- **Polling**: ~33 requests/second (100 users × 1 request/3 seconds)
- **WebSocket**: Minimal overhead (persistent connections)

### Optimization Tips

1. **Use caching** - Cache user lists and chat metadata
2. **Database indexing** - Ensure proper indexes on message queries
3. **Pagination** - Limit messages returned per request
4. **CDN for static files** - Reduce server load

### When to Switch to WebSocket

Consider switching to WebSocket if:

- You have > 500 concurrent users
- Server load becomes an issue
- You need instant message delivery
- You want typing indicators and presence

## Advantages of Polling

### Simplicity

- No Redis installation or management
- No Django Channels configuration
- Works with standard Django deployment

### Compatibility

- Works on any hosting platform
- No special server requirements
- Compatible with all browsers

### Reliability

- Automatic reconnection on network issues
- No WebSocket connection drops
- Works behind restrictive firewalls

## Limitations of Polling

### Delay

Messages appear with a 0-3 second delay (average 1.5 seconds).

### Server Load

More HTTP requests compared to WebSocket.

### Features

Some features work differently:

- **Typing indicators**: Updated every 3 seconds instead of instantly
- **Read receipts**: Updated on next poll instead of immediately
- **Presence**: Updated periodically instead of in real-time

## Switching from Polling to WebSocket

To upgrade from polling to WebSocket later:

1. Follow the [WebSocket Setup Guide](websocket-setup.md)
2. Change `TRANSPORT` setting to `"websocket"`
3. Restart your server

No database changes or data migration required!

## Troubleshooting

### Messages Not Appearing

**Problem**: New messages don't appear in the chat.

**Solutions**:

1. Check browser console for errors
2. Verify `TRANSPORT` is set to `"polling"`
3. Check that the API endpoints are accessible
4. Verify user is authenticated

### High Server Load

**Problem**: Server experiencing high load with polling.

**Solutions**:

1. Implement database query optimization
2. Add caching for frequently accessed data
3. Increase polling interval (requires JavaScript customization)
4. Consider switching to WebSocket

### Polling Requests Failing

**Problem**: Polling requests return errors.

**Solutions**:

1. Check server logs for error details
2. Verify API endpoints are working: `/messages/api/chats/`
3. Check authentication middleware is configured
4. Verify CSRF token handling

## Complete Configuration Example

Here's a complete configuration for polling transport:

```python
# settings.py

DJANGO_MESSAGING = {
    # Use polling transport
    "TRANSPORT": "polling",
    
    # Pagination (keep these reasonable for polling)
    "MESSAGES_PER_PAGE": 50,
    "CHATS_PER_PAGE": 10,
    
    # Layout settings
    "TOP_NAVIGATION_HEIGHT": "64px",
    "CONTENT_TOP_OFFSET": "96px",
    "CONTENT_BOTTOM_OFFSET": "32px",
    
    # Features
    "ENABLE_CHAT_ROOMS": True,
    "ENABLE_DMS": True,
    "SHOW_CHAT_ROOMS_IN_MESSAGES": True,
    "SHOW_DMS_IN_MESSAGES": True,
    
    # Permissions
    "ALLOW_EDITING_OWN_MESSAGES": True,
    "ALLOW_DELETING_OWN_MESSAGES": True,
    "ALLOW_ADDING_REACTIONS": True,
}

# Standard Django settings (no Channels required)
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    
    "rest_framework",
    "django_messaging",
    
    # Your apps
]
```

## Next Steps

- Learn about [Customization](customization.md) options
- Explore [Embedding Widgets](embedding-widgets.md)
- Check the [API Reference](../reference/api.md)
- Review [Configuration Options](../getting-started/configuration.md)
- Consider [WebSocket Setup](websocket-setup.md) for real-time messaging

