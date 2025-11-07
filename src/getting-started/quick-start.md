# Quick Start

This guide will help you get started with Django Messaging quickly. We'll set up basic messaging functionality and send your first message.

## Prerequisites

Make sure you've completed the [Installation](installation.md) steps before proceeding.

## Choose Your Transport Method

Django Messaging supports two transport mechanisms:

1. **WebSocket** (Recommended) - Real-time, instant message delivery
2. **Polling** - Simpler setup, no Redis required

For this quick start, we'll use **polling** to keep things simple. You can switch to WebSocket later by following the [WebSocket Setup Guide](../guides/websocket-setup.md).

## Step 1: Configure Polling Transport

Add this to your `settings.py`:

```python
DJANGO_MESSAGING = {
    "TRANSPORT": "polling",
}
```

## Step 2: Start the Development Server

Start your Django development server:

```bash
python manage.py runserver
```

## Step 3: Access the Messaging Interface

1. Make sure you're logged in to your Django application
2. Navigate to `http://127.0.0.1:8000/messages/`
3. You should see the messaging interface

## Step 4: Create Your First Chat

### Option A: Start a Direct Message

1. Click the **"New chat"** button
2. The Members dialog will open automatically
3. Search for a user by name, username, or email
4. Click on a user to add them to the chat
5. Click **"Save"** to create the chat

### Option B: Create a Group Chat

1. Click the **"New chat"** button
2. Add multiple users from the Members dialog
3. Click **"Save"** to create the group chat
4. Optionally, rename the chat by clicking the chat title

## Step 5: Send Your First Message

1. Select a chat from the chat list on the left
2. Type your message in the input field at the bottom
3. Press **Enter** to send (or click the send button)
4. Your message will appear in the message list

## Step 6: Try Message Features

### React to Messages

1. Hover over any message
2. Click the reaction button (emoji icon)
3. Select an emoji to react

### Edit Your Messages

1. Hover over your own message
2. Click the three-dot menu
3. Select **"Edit"**
4. Modify the message and press Enter

### Delete Messages

1. Hover over your own message
2. Click the three-dot menu
3. Select **"Delete"**
4. Confirm the deletion

## Step 7: Manage Chat Members (Group Chats)

For group chats, you can manage members:

1. Click the **"Members"** button in the chat header
2. Add new members by searching and selecting users
3. Remove members by clicking the remove icon next to their name
4. Click **"Save"** to apply changes

## Using Embedded Widgets

### Embed a DM Widget

Add a direct message widget to a user profile page:

```django
{# user_profile.html #}
{% load static %}

<div class="user-profile">
    <h1>{{ profile_user.get_full_name }}</h1>
    
    {% if MESSAGING_SETTINGS.ENABLE_DMS and request.user != profile_user %}
        {% include "messaging/includes/dm.html" with contact_user=profile_user %}
    {% endif %}
</div>
```

### Embed a Chat Room

Add a chat room to any content object (e.g., a blog post, project, etc.):

```django
{# blog_post_detail.html #}
{% load static %}

<article>
    <h1>{{ post.title }}</h1>
    <div>{{ post.content }}</div>
</article>

{% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS %}
    <section class="chat-section">
        <h2>Discussion</h2>
        {% include "messaging/includes/room.html" with content_object=post %}
    </section>
{% endif %}
```

## Understanding the Interface

### Chat List (Left Panel)

- Shows all your chats (DMs, group chats, and rooms)
- Search chats using the search box
- Click a chat to open it
- Unread chats are highlighted

### Message List (Center Panel)

- Displays messages in chronological order
- Scroll up to load older messages
- Shows typing indicators when others are typing
- Displays read receipts

### Chat Header (Top)

- Shows chat title and participants
- **Members** button to manage participants (group chats)
- **Search** to find messages in the current chat

### Message Input (Bottom)

- Type your message here
- Press **Enter** to send
- Supports multi-line messages (Shift+Enter for new line in some configurations)

## Next Steps

Now that you have basic messaging working, you can:

- **Upgrade to WebSocket**: Follow the [WebSocket Setup Guide](../guides/websocket-setup.md) for real-time messaging
- **Customize the Interface**: Check out the [Customization Guide](../guides/customization.md)
- **Configure Settings**: Explore all [Configuration Options](configuration.md)
- **Embed Widgets**: Learn more about [Embedding Widgets](../guides/embedding-widgets.md)
- **Use the API**: Explore the [API Reference](../reference/api.md)

## Common Questions

### How do I enable WebSocket for real-time messaging?

Follow the [WebSocket Setup Guide](../guides/websocket-setup.md). You'll need to install Redis and configure Django Channels.

### Can I customize the appearance?

Yes! See the [Customization Guide](../guides/customization.md) for details on customizing templates, styles, and behavior.

### How do I control who can message whom?

You can customize the contact list function. See the [Configuration Guide](configuration.md#customizable-functions) for details.

### Can I disable certain features?

Yes, many features can be toggled on/off in the settings. See the [Settings Reference](../reference/settings.md) for all available options.

