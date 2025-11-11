# Frequently Asked Questions

## General Questions

### What is Django Messaging?

Django Messaging is a lightweight, extensible Django app for private messaging. It provides real-time messaging, group chats, and embeddable chat widgets that can be easily integrated into any Django project.

### Is Django Messaging free?

Django Messaging is a commercial product available for purchase from [Gumroad](https://websightful.gumroad.com/l/django-messaging). It's a one-time payment with no recurring subscriptions or domain limitations.

### What license does Django Messaging use?

Django Messaging is released under the MIT License, which allows you to use it in commercial projects, modify it, and distribute it.

### Can I use Django Messaging on multiple websites?

Yes! There are no domain limitations. You can use Django Messaging on as many websites as you need.

## Installation & Setup

### What are the requirements?

- Python 3.8 or higher
- Django 4.2 or higher
- Django REST Framework

For WebSocket support:

- Django Channels 4.0+
- channels-redis 4.0+
- Redis server
- Daphne 4.0+ or Uvicorn 0.20+

### Do I need Redis?

Redis is only required if you want to use WebSocket transport for real-time messaging. If you use polling transport, Redis is not needed.

### Can I use Django Messaging with my existing Django project?

Yes! Django Messaging is designed to integrate seamlessly with existing Django projects. Just follow the [Installation Guide](getting-started/installation.md).

### Does Django Messaging work with custom user models?

Yes, Django Messaging fully supports custom user models configured via the `AUTH_USER_MODEL` setting.

## Features

### What's the difference between WebSocket and polling?

**WebSocket**:

- Real-time, instant message delivery
- Requires Redis and Django Channels
- More complex setup
- Better for high-traffic sites

**Polling**:

- Near real-time (3-second delay)
- No Redis required
- Simple setup
- Better for small to medium sites

See [WebSocket Setup](guides/websocket-setup.md) and [Polling Setup](guides/polling-setup.md) for details.

### Can users edit or delete messages?

Yes, users can edit and delete their own messages. These features can be disabled in settings:

```python
DJANGO_MESSAGING = {
    "ALLOW_EDITING_OWN_MESSAGES": False,
    "ALLOW_DELETING_OWN_MESSAGES": False,
}
```

### Does Django Messaging support emoji reactions?

Yes! Users can react to messages with emojis. This feature can be disabled:

```python
DJANGO_MESSAGING = {
    "ALLOW_ADDING_REACTIONS": False,
}
```

### Can I create group chats?

Yes, Django Messaging supports:

- Direct messages (1-on-1)
- Group chats (multiple users)
- Chat rooms (attached to content objects)

### Does it support read receipts?

Yes, Django Messaging tracks which users have read each message and displays read receipts.

### Are there typing indicators?

Yes, typing indicators show when other users are typing in a chat.

## Customization

### Can I customize the appearance?

Yes! Django Messaging is highly customizable:

- Uses Tailwind CSS for easy styling
- Override Tailwind classes or add custom CSS
- Override templates for complete control
- Built-in dark mode support with Tailwind's `dark:` variant

See the [Customization Guide](guides/customization.md).

### Can I control who can message whom?

Yes, you can customize the contact list function to control which users can message each other:

```python
DJANGO_MESSAGING = {
    "GET_CONTACT_LIST_FUNCTION": "myapp.utils.get_contact_list",
}
```

See [Configuration](getting-started/configuration.md#customizable-functions).

### Can I use my own user avatars?

Yes, customize the avatar function to use your own user profile images:

```python
DJANGO_MESSAGING = {
    "GET_USER_AVATAR_URL_FUNCTION": "myapp.utils.get_user_avatar_url",
}
```

### Can I change how user names are displayed?

Yes, customize the user name function:

```python
DJANGO_MESSAGING = {
    "GET_USER_NAME_FUNCTION": "myapp.utils.get_user_name",
}
```

## Deployment

### How do I deploy Django Messaging in production?

For **polling transport**, use any standard WSGI server (Gunicorn, uWSGI, etc.).

For **WebSocket transport**, use an ASGI server:

- Daphne
- Uvicorn
- Gunicorn with Uvicorn workers

See [WebSocket Setup](guides/websocket-setup.md#production-deployment).

### Do I need a special server for WebSocket?

Yes, WebSocket requires an ASGI server. However, you can use polling transport with standard WSGI servers if you prefer.

### Can I use Django Messaging on shared hosting?

Yes, if you use polling transport. WebSocket may not be available on all shared hosting platforms.

### How do I handle static files?

Use Django's standard static file handling:

```bash
python manage.py collectstatic
```

Then serve static files with your web server (Nginx, Apache, etc.) or a CDN.

## Performance

### How many concurrent users can Django Messaging handle?

This depends on your infrastructure and transport method:

**Polling**: Suitable for small to medium sites (< 500 concurrent users)
**WebSocket**: Can handle thousands of concurrent users with proper infrastructure

### Will Django Messaging slow down my site?

No, Django Messaging is designed to be lightweight and efficient:

- Lazy loading of messages
- Efficient database queries
- Pagination
- Optional caching

### Should I use caching?

Yes, consider caching for:

- User lists
- Chat metadata
- Frequently accessed data

Use Django's caching framework with Redis or Memcached.

## Troubleshooting

### Messages aren't appearing in real-time

**For WebSocket**:
1. Verify Redis is running: `redis-cli ping`
2. Check WebSocket connection in browser console
3. Verify `TRANSPORT` is set to `"websocket"`

**For Polling**:
1. Check browser console for errors
2. Verify API endpoints are accessible
3. Check that polling is working (Network tab)

### I get "WebSocket connection failed"

1. Verify Redis is running
2. Check `CHANNEL_LAYERS` configuration
3. Ensure `ASGI_APPLICATION` is set correctly
4. Check that you're using an ASGI server (Daphne/Uvicorn)

### Static files aren't loading

1. Run `python manage.py collectstatic`
2. Check `STATIC_URL` and `STATIC_ROOT` settings
3. Verify web server is serving static files correctly

### I can't see the messaging interface

1. Verify you're logged in
2. Check that `django_messaging` is in `INSTALLED_APPS`
3. Verify URLs are configured correctly
4. Check browser console for JavaScript errors

## Integration

### Can I embed chat widgets in my pages?

Yes! Django Messaging provides two embeddable widgets:

- DM widget for user profiles
- Room widget for content objects

See [Embedding Widgets](guides/embedding-widgets.md).

### Can I attach chat rooms to my models?

Yes, you can attach a chat room to any Django model instance:

```django
{% include "messaging/includes/room.html" with content_object=my_object %}
```

### Does Django Messaging have an API?

Yes, Django Messaging provides a complete REST API for all messaging operations. See the [API Reference](reference/api.md).

### Can I use Django Messaging with React/Vue/Angular?

Yes, you can use the REST API to build your own frontend with any JavaScript framework.

## Internationalization

### Is Django Messaging translatable?

Yes, Django Messaging is built with internationalization in mind. Use Django's standard translation workflow:

```bash
python manage.py makemessages -l es
python manage.py compilemessages
```

### What languages are supported?

Django Messaging includes English by default. You can add translations for any language using Django's translation system.

## Support

### Where can I get help?

For technical questions, feature requests, or bug reports, contact Aidas Bendoraitis at <https://www.djangotricks.com/feedback/>.

### Is there a community forum?

Currently, support is provided directly by the developer. A community forum may be added in the future.

### Can I request features?

Yes! Feature requests are welcome. Contact the developer with your suggestions.

### Do you offer custom development?

Yes, custom development services may be available. Contact the developer to discuss your needs.

## Upgrading

### How do I upgrade to a new version?

1. Download the new wheel file
2. Install it: `pip install django_messaging-0.20.0-py3-none-any.whl --upgrade`
3. Run migrations: `python manage.py migrate`
4. Collect static files: `python manage.py collectstatic`
5. Restart your server

### Will upgrading break my existing chats?

No, upgrades are designed to be backward compatible. Your existing chats and messages will be preserved.

### Where can I see what's new?

Check the [Changelog](CHANGELOG.md) for a list of all changes in each version.

## Still Have Questions?

If your question isn't answered here, please contact Aidas Bendoraitis at <https://www.djangotricks.com/feedback/>.

