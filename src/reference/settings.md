# Settings Reference

Complete reference for all Django Messaging configuration options.

## Configuration Dictionary

All settings are configured through the `DJANGO_MESSAGING` dictionary in your `settings.py`:

```python
DJANGO_MESSAGING = {
    # Your settings here
}
```

## Transport Settings

### TRANSPORT

**Type**: `str`  
**Default**: `"websocket"`  
**Options**: `"websocket"`, `"polling"`

Determines the transport mechanism for message delivery.

```python
DJANGO_MESSAGING = {
    "TRANSPORT": "websocket",
}
```

- **`"websocket"`**: Real-time message delivery using WebSocket. Requires Django Channels and Redis.
- **`"polling"`**: HTTP polling for message delivery. Simpler setup, no Redis required.

**See also**: [WebSocket Setup](../guides/websocket-setup.md), [Polling Setup](../guides/polling-setup.md)

## Pagination Settings

### MESSAGES_PER_PAGE

**Type**: `int`  
**Default**: `50`  
**Range**: `1` to `100` (recommended)

Number of messages to load per page.

```python
DJANGO_MESSAGING = {
    "MESSAGES_PER_PAGE": 50,
}
```

Higher values load more messages at once but may impact performance.

### CHATS_PER_PAGE

**Type**: `int`  
**Default**: `10`  
**Range**: `1` to `50` (recommended)

Number of chats to display per page in the chat list.

```python
DJANGO_MESSAGING = {
    "CHATS_PER_PAGE": 10,
}
```

## Layout Settings

### TOP_NAVIGATION_HEIGHT

**Type**: `str` (CSS value)  
**Default**: `"64px"`

Height of your site's top navigation bar.

```python
DJANGO_MESSAGING = {
    "TOP_NAVIGATION_HEIGHT": "64px",
}
```

Used to calculate the correct positioning of the messaging interface.

### CONTENT_TOP_OFFSET

**Type**: `str` (CSS value)  
**Default**: `"96px"`

Total offset from the viewport top, including navigation height and margins.

```python
DJANGO_MESSAGING = {
    "CONTENT_TOP_OFFSET": "96px",
}
```

Should be `TOP_NAVIGATION_HEIGHT` plus any additional top margins.

### CONTENT_BOTTOM_OFFSET

**Type**: `str` (CSS value)  
**Default**: `"32px"`

Bottom padding/margins for the messaging interface.

```python
DJANGO_MESSAGING = {
    "CONTENT_BOTTOM_OFFSET": "32px",
}
```

## Feature Toggles

### SHOW_DELETED_MESSAGE_INDICATORS

**Type**: `bool`  
**Default**: `False`

Show or hide indicators for deleted messages.

```python
DJANGO_MESSAGING = {
    "SHOW_DELETED_MESSAGE_INDICATORS": False,
}
```

- **`True`**: Deleted messages show as "This message was deleted"
- **`False`**: Deleted messages are completely removed from view

### ENABLE_CHAT_ROOMS

**Type**: `bool`  
**Default**: `True`

Enable or disable embedded chat room functionality.

```python
DJANGO_MESSAGING = {
    "ENABLE_CHAT_ROOMS": True,
}
```

When disabled, the room widget will not be available.

### ENABLE_DMS

**Type**: `bool`  
**Default**: `True`

Enable or disable embedded DM widget functionality.

```python
DJANGO_MESSAGING = {
    "ENABLE_DMS": True,
}
```

When disabled, the DM widget will not be available.

### SHOW_CHAT_ROOMS_IN_MESSAGES

**Type**: `bool`  
**Default**: `True`

Show or hide chat rooms in the main messages page chat list.

```python
DJANGO_MESSAGING = {
    "SHOW_CHAT_ROOMS_IN_MESSAGES": True,
}
```

Useful if you want rooms to be accessible only through embedded widgets.

### SHOW_DMS_IN_MESSAGES

**Type**: `bool`  
**Default**: `True`

Show or hide DMs in the main messages page chat list.

```python
DJANGO_MESSAGING = {
    "SHOW_DMS_IN_MESSAGES": True,
}
```

Useful if you want DMs to be accessible only through embedded widgets.

## Permission Settings

### ALLOW_RENAMING_CHAT_ROOMS

**Type**: `bool`  
**Default**: `True`

Allow users to rename chat rooms.

```python
DJANGO_MESSAGING = {
    "ALLOW_RENAMING_CHAT_ROOMS": True,
}
```

### ALLOW_RENAMING_DMS

**Type**: `bool`  
**Default**: `True`

Allow users to rename direct message chats.

```python
DJANGO_MESSAGING = {
    "ALLOW_RENAMING_DMS": True,
}
```

### ALLOW_EDITING_OWN_MESSAGES

**Type**: `bool`  
**Default**: `True`

Allow users to edit their own messages.

```python
DJANGO_MESSAGING = {
    "ALLOW_EDITING_OWN_MESSAGES": True,
}
```

When disabled, the edit option will not appear in message context menus.

### ALLOW_DELETING_OWN_MESSAGES

**Type**: `bool`  
**Default**: `True`

Allow users to delete their own messages.

```python
DJANGO_MESSAGING = {
    "ALLOW_DELETING_OWN_MESSAGES": True,
}
```

When disabled, the delete option will not appear in message context menus.

### ALLOW_ADDING_REACTIONS

**Type**: `bool`  
**Default**: `True`

Allow users to add emoji reactions to messages.

```python
DJANGO_MESSAGING = {
    "ALLOW_ADDING_REACTIONS": True,
}
```

When disabled, the reaction button will not appear on messages.

## Customizable Functions

### GET_CONTACT_LIST_FUNCTION

**Type**: `str` (dotted path to function)  
**Default**: `"django_messaging.defaults.get_contact_list"`

Function that returns a queryset of users for the contact list.

```python
DJANGO_MESSAGING = {
    "GET_CONTACT_LIST_FUNCTION": "myapp.utils.get_contact_list",
}
```

**Function signature**:

```python
def get_contact_list(user):
    """
    Return a queryset of users that the given user can message.
    
    Args:
        user: The current user
        
    Returns:
        QuerySet of User objects
    """
    return User.objects.filter(is_active=True).exclude(pk=user.pk)
```

**Default behavior**: Returns all active users except the current user.

### GET_USER_NAME_FUNCTION

**Type**: `str` (dotted path to function)  
**Default**: `"django_messaging.defaults.get_user_name"`

Function that returns the display name for a user.

```python
DJANGO_MESSAGING = {
    "GET_USER_NAME_FUNCTION": "myapp.utils.get_user_name",
}
```

**Function signature**:

```python
def get_user_name(user):
    """
    Return the display name for a user.
    
    Args:
        user: User object
        
    Returns:
        str: Display name
    """
    return user.get_full_name() or user.username
```

**Default behavior**: Returns full name if available, otherwise username.

### GET_USER_AVATAR_URL_FUNCTION

**Type**: `str` (dotted path to function)  
**Default**: `"django_messaging.defaults.get_user_avatar_url"`

Function that returns the avatar URL for a user.

```python
DJANGO_MESSAGING = {
    "GET_USER_AVATAR_URL_FUNCTION": "myapp.utils.get_user_avatar_url",
}
```

**Function signature**:

```python
def get_user_avatar_url(user):
    """
    Return the avatar URL for a user.
    
    Args:
        user: User object
        
    Returns:
        str: Avatar URL (can be a data URL or regular URL)
    """
    # Return SVG data URL with user initials
    return "data:image/svg+xml,..."
```

**Default behavior**: Generates an SVG data URL with user initials and a consistent color based on the user's ID.

## Complete Example

Here's a complete configuration example:

```python
DJANGO_MESSAGING = {
    # Transport
    "TRANSPORT": "websocket",
    
    # Pagination
    "MESSAGES_PER_PAGE": 50,
    "CHATS_PER_PAGE": 10,
    
    # Layout
    "TOP_NAVIGATION_HEIGHT": "64px",
    "CONTENT_TOP_OFFSET": "96px",
    "CONTENT_BOTTOM_OFFSET": "32px",
    
    # Features
    "SHOW_DELETED_MESSAGE_INDICATORS": False,
    "ENABLE_CHAT_ROOMS": True,
    "ENABLE_DMS": True,
    "SHOW_CHAT_ROOMS_IN_MESSAGES": True,
    "SHOW_DMS_IN_MESSAGES": True,
    
    # Permissions
    "ALLOW_RENAMING_CHAT_ROOMS": True,
    "ALLOW_RENAMING_DMS": True,
    "ALLOW_EDITING_OWN_MESSAGES": True,
    "ALLOW_DELETING_OWN_MESSAGES": True,
    "ALLOW_ADDING_REACTIONS": True,
    
    # Customization
    "GET_CONTACT_LIST_FUNCTION": "django_messaging.defaults.get_contact_list",
    "GET_USER_NAME_FUNCTION": "django_messaging.defaults.get_user_name",
    "GET_USER_AVATAR_URL_FUNCTION": "django_messaging.defaults.get_user_avatar_url",
}
```

## Context Processor

Don't forget to add the context processor to make settings available in templates:

```python
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "OPTIONS": {
            "context_processors": [
                # ...
                "django_messaging.context_processors.messaging_settings",
            ],
        },
    },
]
```

This makes `MESSAGING_SETTINGS` available in all templates.

## Next Steps

- Learn about [Customization](../guides/customization.md)
- Explore the [API Reference](api.md)
- Check [Template Tags](template-tags.md)

