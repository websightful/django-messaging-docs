# Customization

Django Messaging is designed to be highly customizable. This guide covers various ways to customize the appearance and behavior of the messaging interface.

## Template Customization

### Override Templates

You can override any template by creating your own version in your project's template directory. Django's template loading system will use your templates instead of the app's defaults.

```
myproject/
    templates/
        messaging/
            base.html              # Override base template
            messaging.html         # Override main chat page
            includes/
                dm.html            # Override DM widget
                room.html          # Override chat room widget
                button.html        # Override button component
                context_menu_item.html  # Override context menu items
```

### Key Templates

#### Base Template

Override `messaging/base.html` to integrate with your site's layout:

```django
{# templates/messaging/base.html #}
{% extends "site_base.html" %}

{% block title %}Messages{% endblock %}

{% block content %}
    {# Messaging interface content #}
{% endblock %}
```

#### Chat Page

Override `messaging/messaging.html` to customize the main messaging interface.

#### DM Widget

Override `messaging/includes/dm.html` to customize the embedded DM widget:

```django
{# templates/messaging/includes/dm.html #}
<div class="custom-dm-widget">
    {# Your custom DM widget HTML #}
</div>
```

#### Room Widget

Override `messaging/includes/room.html` to customize the embedded chat room:

```django
{# templates/messaging/includes/room.html #}
<div class="custom-room-widget">
    {# Your custom room widget HTML #}
</div>
```

## Styling Customization

Django Messaging uses **Tailwind CSS** for styling with built-in dark mode support.

### Tailwind CSS Integration

The app uses Tailwind CSS with the `class` dark mode strategy. To enable dark mode in your project, add the `dark` class to the `<html>` or `<body>` element:

```html
<html class="dark">
    <!-- Dark mode enabled -->
</html>
```

Or toggle it dynamically with JavaScript:

```javascript
// Toggle dark mode
document.documentElement.classList.toggle('dark');

// Enable dark mode
document.documentElement.classList.add('dark');

// Disable dark mode
document.documentElement.classList.remove('dark');
```

### Dark Mode

Django Messaging includes built-in dark mode support using Tailwind's `dark:` variant. The app automatically adapts to the dark mode state when the `dark` class is present on the document element.

Key dark mode styles include:

- **Backgrounds**: `dark:bg-gray-800`, `dark:bg-gray-900`
- **Text**: `dark:text-gray-100`, `dark:text-gray-400`
- **Borders**: `dark:border-gray-700`
- **Custom components**: Reactions, messages, and search results have specific dark mode styles

### Customizing Colors

Django Messaging loads Tailwind CSS from CDN and uses inline utility classes. To customize the appearance, create a custom stylesheet and override specific components:

```css
/* Custom dark mode colors for reactions */
.dark .reaction {
    background-color: #1e293b;
    border-color: #334155;
}

/* Custom search result highlighting */
.dark .search-result {
    border-color: #f59e0b;
    background-color: #451a03;
}

/* Custom message colors */
.dark .message-deleted .message-content {
    color: #9ca3af;
}

/* Override message bubble colors */
.message-bubble-own {
    background-color: #3b82f6 !important;
}

.message-bubble-other {
    background-color: #f3f4f6 !important;
}

.dark .message-bubble-other {
    background-color: #374151 !important;
}
```

!!! note "Why Custom CSS?"
    Django Messaging uses Tailwind CSS from CDN, which means you cannot extend a `tailwind.config.js` file. All customization must be done through custom CSS that overrides the default styles.

### Custom CSS Classes

Add custom CSS classes to specific elements using the template system:

```django
{# In your custom template #}
<div class="messaging-container my-custom-class">
    {# Content #}
</div>
```

### External Stylesheet

Link your custom stylesheet in the base template:

```django
{% block extra_css %}
    <link rel="stylesheet" href="{% static 'css/custom-messaging.css' %}">
{% endblock %}
```

## Customizable Functions

### User Display Name

Customize how user names are displayed:

```python
# myapp/utils.py
def get_user_name(user):
    """Return custom display name"""
    if hasattr(user, "profile"):
        return user.profile.display_name
    return user.get_full_name() or user.username

# settings.py
DJANGO_MESSAGING = {
    "GET_USER_NAME_FUNCTION": "myapp.utils.get_user_name",
}
```

### User Avatar

Customize user avatars:

```python
# myapp/utils.py
def get_user_avatar_url(user):
    """Return user avatar URL"""
    if hasattr(user, "profile") and user.profile.avatar:
        return user.profile.avatar.url
    
    # Fallback to default
    from django_messaging.defaults import get_user_avatar_url as default_avatar
    return default_avatar(user)

# settings.py
DJANGO_MESSAGING = {
    "GET_USER_AVATAR_URL_FUNCTION": "myapp.utils.get_user_avatar_url",
}
```

### Contact List

Customize who can message whom:

```python
# myapp/utils.py
from django.contrib.auth import get_user_model

User = get_user_model()

def get_contact_list(user):
    """Return users in the same organization"""
    if not hasattr(user, "organization"):
        return User.objects.none()
    
    return User.objects.filter(
        organization=user.organization,
        is_active=True
    ).exclude(pk=user.pk)

# settings.py
DJANGO_MESSAGING = {
    "GET_CONTACT_LIST_FUNCTION": "myapp.utils.get_contact_list",
}
```

## Layout Customization

### Adjust for Your Navigation

Configure layout offsets to match your site's navigation:

```python
DJANGO_MESSAGING = {
    # Height of your top navigation
    "TOP_NAVIGATION_HEIGHT": "64px",
    
    # Total offset from top (navigation + margins)
    "CONTENT_TOP_OFFSET": "96px",
    
    # Bottom padding
    "CONTENT_BOTTOM_OFFSET": "32px",
}
```

### Responsive Breakpoints

Customize responsive behavior by overriding CSS:

```css
/* Custom breakpoints */
@media (max-width: 768px) {
    .messaging-container {
        /* Mobile styles */
    }
}

@media (min-width: 769px) and (max-width: 1024px) {
    .messaging-container {
        /* Tablet styles */
    }
}

@media (min-width: 1025px) {
    .messaging-container {
        /* Desktop styles */
    }
}
```

## Feature Customization

### Disable Features

Disable features you don't need:

```python
DJANGO_MESSAGING = {
    "ALLOW_EDITING_OWN_MESSAGES": False,
    "ALLOW_DELETING_OWN_MESSAGES": False,
    "ALLOW_ADDING_REACTIONS": False,
    "ALLOW_RENAMING_CHAT_ROOMS": False,
    "ALLOW_RENAMING_DMS": False,
}
```

### Hide UI Elements

Hide specific UI elements:

```python
DJANGO_MESSAGING = {
    "SHOW_DELETED_MESSAGE_INDICATORS": False,
    "SHOW_CHAT_ROOMS_IN_MESSAGES": False,
    "SHOW_DMS_IN_MESSAGES": False,
}
```

## Internationalization

### Add Translations

Django Messaging is translation-ready. Add your translations:

```bash
# Create locale directory
mkdir -p locale

# Generate message files
python manage.py makemessages -l es
python manage.py makemessages -l fr
python manage.py makemessages -l de

# Edit .po files in locale/*/LC_MESSAGES/django.po

# Compile messages
python manage.py compilemessages
```

### Custom Text

Override specific text by creating custom templates or using translation overrides.

## JavaScript Customization

!!! warning "No Custom Events API"
    Django Messaging does not currently dispatch custom JavaScript events. The messaging functionality is handled internally by the `ChatApp`, `DMApp`, and `RoomApp` classes. To extend functionality, you would need to modify the JavaScript files directly or override the templates.

### Accessing the Chat Instance

The main chat application instance is available globally:

```javascript
// Access the chat app instance (on the messages page)
const chatApp = window.chatApp;

// Access current chat ID
console.log(chatApp.currentChatId);

// Access chat data
console.log(chatApp.chats);
```

### Extending via Template Overrides

To add custom JavaScript functionality, override the messaging templates and add your own scripts:

```django
{# templates/messaging/messaging.html #}
{% extends "messaging/messaging.html" %}

{% block extra_body %}
    {{ block.super }}
    <script>
        // Add your custom JavaScript here
        // You can access window.chatApp after DOMContentLoaded
        document.addEventListener('DOMContentLoaded', () => {
            console.log('Chat app initialized:', window.chatApp);

            // Example: Monitor when messages are sent
            // (You would need to modify the ChatApp class to add hooks)
        });
    </script>
{% endblock %}
```

## Advanced Customization

### Custom Message Rendering

Override message rendering by customizing templates:

```django
{# templates/messaging/includes/message.html #}
<div class="message {% if message.is_own %}message-own{% endif %}">
    <div class="message-avatar">
        <img src="{{ message.sender.avatar_url }}" alt="{{ message.sender.name }}">
    </div>
    <div class="message-content">
        <div class="message-header">
            <span class="message-sender">{{ message.sender.name }}</span>
            <span class="message-time">{{ message.created_at|date:"H:i" }}</span>
        </div>
        <div class="message-body">
            {{ message.content|linebreaks }}
        </div>
    </div>
</div>
```

### Custom Chat List Item

Customize how chats appear in the list:

```django
{# templates/messaging/includes/chat_list_item.html #}
<div class="chat-item {% if chat.has_unread %}chat-unread{% endif %}">
    <div class="chat-avatar">
        {# Custom avatar rendering #}
    </div>
    <div class="chat-info">
        <div class="chat-title">{{ chat.title }}</div>
        <div class="chat-preview">{{ chat.last_message.content|truncatewords:10 }}</div>
    </div>
    <div class="chat-meta">
        <div class="chat-time">{{ chat.last_message.created_at|timesince }}</div>
        {% if chat.unread_count %}
            <div class="chat-badge">{{ chat.unread_count }}</div>
        {% endif %}
    </div>
</div>
```

## Next Steps

- Explore the [API Reference](../reference/api.md) for programmatic customization
- Check [Template Tags](../reference/template-tags.md) for available template helpers
- Review [Settings Reference](../reference/settings.md) for all configuration options

