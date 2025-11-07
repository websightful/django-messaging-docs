# Configuration

Django Messaging can be customized through the `DJANGO_MESSAGING` setting in your `settings.py`. This guide covers all available configuration options.

## Basic Configuration

Add the `DJANGO_MESSAGING` dictionary to your `settings.py`:

```python
DJANGO_MESSAGING = {
    # Your configuration options here
}
```

## Transport Settings

### TRANSPORT

Choose between WebSocket and polling for message delivery.

- **Type**: `str`
- **Default**: `"websocket"`
- **Options**: `"websocket"` or `"polling"`

```python
DJANGO_MESSAGING = {
    "TRANSPORT": "websocket",  # or "polling"
}
```

**WebSocket** provides real-time, instant message delivery but requires Redis and Django Channels. See [WebSocket Setup](../guides/websocket-setup.md).

**Polling** is simpler to set up and doesn't require Redis, but messages are delivered with a slight delay. See [Polling Setup](../guides/polling-setup.md).

## Pagination Settings

### MESSAGES_PER_PAGE

Number of messages to load per page.

- **Type**: `int`
- **Default**: `50`

```python
DJANGO_MESSAGING = {
    "MESSAGES_PER_PAGE": 50,
}
```

### CHATS_PER_PAGE

Number of chats to display per page in the chat list.

- **Type**: `int`
- **Default**: `10`

```python
DJANGO_MESSAGING = {
    "CHATS_PER_PAGE": 10,
}
```

## Layout Settings

These settings control the layout of the full-screen messaging interface.

### TOP_NAVIGATION_HEIGHT

Height of your site's top navigation bar.

- **Type**: `str` (CSS value)
- **Default**: `"64px"`

```python
DJANGO_MESSAGING = {
    "TOP_NAVIGATION_HEIGHT": "64px",
}
```

### CONTENT_TOP_OFFSET

Total offset from the viewport top (includes navigation height and any margins).

- **Type**: `str` (CSS value)
- **Default**: `"96px"`

```python
DJANGO_MESSAGING = {
    "CONTENT_TOP_OFFSET": "96px",
}
```

### CONTENT_BOTTOM_OFFSET

Bottom padding/margins for the messaging interface.

- **Type**: `str` (CSS value)
- **Default**: `"32px"`

```python
DJANGO_MESSAGING = {
    "CONTENT_BOTTOM_OFFSET": "32px",
}
```

## Feature Toggles

### SHOW_DELETED_MESSAGE_INDICATORS

Show or hide indicators for deleted messages.

- **Type**: `bool`
- **Default**: `False`

```python
DJANGO_MESSAGING = {
    "SHOW_DELETED_MESSAGE_INDICATORS": False,
}
```

When `True`, deleted messages show as "This message was deleted" instead of being completely removed.

### ENABLE_CHAT_ROOMS

Enable or disable embedded chat room functionality.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ENABLE_CHAT_ROOMS": True,
}
```

### ENABLE_DMS

Enable or disable embedded DM widget functionality.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ENABLE_DMS": True,
}
```

### SHOW_CHAT_ROOMS_IN_MESSAGES

Show or hide chat rooms in the main messages page chat list.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "SHOW_CHAT_ROOMS_IN_MESSAGES": True,
}
```

### SHOW_DMS_IN_MESSAGES

Show or hide DMs in the main messages page chat list.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "SHOW_DMS_IN_MESSAGES": True,
}
```

## Permission Settings

### ALLOW_RENAMING_CHAT_ROOMS

Allow users to rename chat rooms.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ALLOW_RENAMING_CHAT_ROOMS": True,
}
```

### ALLOW_RENAMING_DMS

Allow users to rename direct message chats.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ALLOW_RENAMING_DMS": True,
}
```

### ALLOW_EDITING_OWN_MESSAGES

Allow users to edit their own messages.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ALLOW_EDITING_OWN_MESSAGES": True,
}
```

### ALLOW_DELETING_OWN_MESSAGES

Allow users to delete their own messages.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ALLOW_DELETING_OWN_MESSAGES": True,
}
```

### ALLOW_ADDING_REACTIONS

Allow users to add emoji reactions to messages.

- **Type**: `bool`
- **Default**: `True`

```python
DJANGO_MESSAGING = {
    "ALLOW_ADDING_REACTIONS": True,
}
```

## Customizable Functions

You can override default functions to customize behavior.

### GET_CONTACT_LIST_FUNCTION

Function that returns a queryset of users for the contact list.

- **Type**: `str` (dotted path to function)
- **Default**: `"django_messaging.defaults.get_contact_list"`

```python
DJANGO_MESSAGING = {
    "GET_CONTACT_LIST_FUNCTION": "myapp.utils.get_contact_list",
}
```

**Default behavior**: Returns all active users except the current user.

**Custom implementation example**:

```python
# myapp/utils.py
def get_contact_list(user):
    """Return only users in the same organization"""
    if not hasattr(user, "organization"):
        return User.objects.none()
    
    return User.objects.filter(
        organization=user.organization,
        is_active=True
    ).exclude(pk=user.pk)
```

### GET_USER_NAME_FUNCTION

Function that returns the display name for a user.

- **Type**: `str` (dotted path to function)
- **Default**: `"django_messaging.defaults.get_user_name"`

```python
DJANGO_MESSAGING = {
    "GET_USER_NAME_FUNCTION": "myapp.utils.get_user_name",
}
```

**Default behavior**: Returns full name if available, otherwise username.

**Custom implementation example**:

```python
# myapp/utils.py
def get_user_name(user):
    """Return user's display name from profile"""
    if hasattr(user, "profile") and user.profile.display_name:
        return user.profile.display_name
    return user.get_full_name() or user.username
```

### GET_USER_AVATAR_URL_FUNCTION

Function that returns the avatar URL for a user.

- **Type**: `str` (dotted path to function)
- **Default**: `"django_messaging.defaults.get_user_avatar_url"`

```python
DJANGO_MESSAGING = {
    "GET_USER_AVATAR_URL_FUNCTION": "myapp.utils.get_user_avatar_url",
}
```

**Default behavior**: Generates an SVG data URL with user initials and a consistent color.

**Custom implementation example**:

```python
# myapp/utils.py
def get_user_avatar_url(user):
    """Return actual image URL if user has a profile picture"""
    if hasattr(user, "profile") and user.profile.avatar:
        return user.profile.avatar.url
    
    # Fallback to default SVG generation
    from django_messaging.defaults import get_user_avatar_url as default_avatar
    return default_avatar(user)
```

## Complete Example

Here's a complete configuration example with commonly used settings:

```python
DJANGO_MESSAGING = {
    # Transport
    "TRANSPORT": "websocket",
    
    # Pagination
    "MESSAGES_PER_PAGE": 50,
    "CHATS_PER_PAGE": 10,
    
    # Layout (adjust to match your site's navigation)
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

## Next Steps

- Learn about [WebSocket Setup](../guides/websocket-setup.md) for real-time messaging
- Explore [Customization Options](../guides/customization.md) for UI customization
- Check the [Settings Reference](../reference/settings.md) for detailed information

