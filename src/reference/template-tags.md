# Template Tags Reference

Django Messaging provides template tags and filters for use in your templates.

## Loading Template Tags

Load the template tags in your template:

```django
{% load messaging_tags %}
```


### get_room_for_object

Get or create a chat room for a content object.

**Syntax**:

```django
{% get_room_for_object content_object as variable_name %}
```

**Parameters**:

- `content_object`: Any Django model instance
- `variable_name`: Variable name to store the room object

**Example**:

```django
{% load messaging_tags %}

{% get_room_for_object post as room %}

<div class="room-info">
    <h3>Discussion for: {{ post.title }}</h3>
    <p>Room ID: {{ room.id }}</p>
    <p>Members: {{ room.members.count }}</p>
</div>
```



## Template Filters

Load the template filters in your template:

```django
{% load messaging_filters %}
```

### get_user_display_name

Get the display name for a user using the configured function.

**Syntax**:

```django
{{ user|get_user_display_name }}
```

**Example**:

```django
{% load messaging_filters %}

<p>Welcome, {{ request.user|get_user_display_name }}!</p>
```

### get_user_avatar_url

Get the avatar URL for a user using the configured function.

**Syntax**:

```django
{{ user|get_user_avatar_url }}
```

**Example**:

```django
{% load messaging_filters %}

<img src="{{ request.user|get_user_avatar_url }}" alt="{{ request.user|get_user_display_name }}">
```

## Context Processor

The `messaging_settings` context processor makes settings available in all templates.

**Add to settings**:

```python
TEMPLATES = [
    {
        "OPTIONS": {
            "context_processors": [
                # ...
                "django_messaging.context_processors.messaging_settings",
            ],
        },
    },
]
```

**Usage in templates**:

```django
{% if MESSAGING_SETTINGS.ENABLE_DMS %}
    <a href="{% url 'messaging:messages' %}">Messages</a>
{% endif %}

{% if MESSAGING_SETTINGS.ALLOW_EDITING_OWN_MESSAGES %}
    <button>Edit Message</button>
{% endif %}
```

**Available settings**:

- `MESSAGING_SETTINGS.TRANSPORT`
- `MESSAGING_SETTINGS.ENABLE_DMS`
- `MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS`
- `MESSAGING_SETTINGS.SHOW_DMS_IN_MESSAGES`
- `MESSAGING_SETTINGS.SHOW_CHAT_ROOMS_IN_MESSAGES`
- `MESSAGING_SETTINGS.ALLOW_EDITING_OWN_MESSAGES`
- `MESSAGING_SETTINGS.ALLOW_DELETING_OWN_MESSAGES`
- `MESSAGING_SETTINGS.ALLOW_ADDING_REACTIONS`
- `MESSAGING_SETTINGS.ALLOW_RENAMING_CHAT_ROOMS`
- `MESSAGING_SETTINGS.ALLOW_RENAMING_DMS`

## Complete Example

Here's a complete example using multiple template tags and filters:

```django
{% load messaging_tags %}
{% load messaging_filters %}

<!DOCTYPE html>
<html>
<head>
    <title>My Site</title>
</head>
<body>
    <nav>
        <ul>
            <li>
                <a href="{% url 'messaging:messages' %}">
                    Messages
                </a>
            </li>
        </ul>

        <div class="user-info">
            <img src="{{ request.user|get_user_avatar_url }}" alt="Avatar">
            <span>{{ request.user|get_user_display_name }}</span>
        </div>
    </nav>

    <main>
        {% if MESSAGING_SETTINGS.ENABLE_DMS and profile_user %}
            <section class="dm-section">
                <h2>Send a Message</h2>
                {% include "messaging/includes/dm.html" with contact_user=profile_user %}
            </section>
        {% endif %}

        {% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS and post %}
            <section class="discussion-section">
                <h2>Discussion</h2>
                {% get_room_for_object post as room %}
                <p>{{ room.members.count }} participants</p>
                {% include "messaging/includes/room.html" with content_object=post %}
            </section>
        {% endif %}
    </main>
</body>
</html>
```

## Custom Template Tags

You can create your own template tags that work with Django Messaging:

```python
# myapp/templatetags/my_messaging_tags.py
from django import template
from django_messaging.models import ChatRoom, ChatMembership

register = template.Library()

@register.simple_tag
def get_active_chats(user):
    """Get chats with recent activity"""
    return ChatRoom.objects.filter(
        members=user
    ).order_by('-updated_at')[:5]

@register.filter
def is_chat_admin(user, chat):
    """Check if user is admin of a chat"""
    return ChatMembership.objects.filter(
        chat=chat,
        user=user,
        role=ChatMembership.Role.ADMIN
    ).exists()
```

**Usage**:

```django
{% load my_messaging_tags %}

{% get_active_chats request.user as active_chats %}
{% for chat in active_chats %}
    <div class="chat-item">
        {{ chat.title }}
        {% if request.user|is_chat_admin:chat %}
            <span class="admin-badge">Admin</span>
        {% endif %}
    </div>
{% endfor %}
```

## Next Steps

- Check the [API Reference](api.md) for programmatic access
- Review [Settings Reference](settings.md) for configuration
- Explore [Models](models.md) for data structure

