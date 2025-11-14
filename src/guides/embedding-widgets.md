# Embedding Widgets

Django Messaging provides reusable widgets that can be embedded anywhere in your Django application. This guide covers how to use the DM widget and chat room widget.

## Overview

Django Messaging offers two embeddable widgets:

1. **DM Widget** - Direct message widget for user profiles
2. **Room Widget** - Chat room widget for content objects

Both widgets are self-contained and can be easily integrated into your existing templates.

## DM Widget

The DM widget allows users to send direct messages from another user's profile page.

### Basic Usage

Include the DM widget in a user profile template:

```django
{# user_profile.html #}
{% load static %}

<div class="user-profile">
    <h1>{{ profile_user.get_full_name }}</h1>
    <p>{{ profile_user.email }}</p>

    {% if MESSAGING_SETTINGS.ENABLE_DMS and request.user != profile_user %}
        {% include "messaging/includes/dm.html" with contact_user=profile_user %}
    {% endif %}
</div>
```

**Note**: The DM widget automatically handles authentication:
- For **authenticated users**: Shows a chat toggle button that opens the messaging widget
- For **unauthenticated users**: Shows a login link that redirects to the login page with `?next=` parameter to return to the current page after login

### Required Context

The DM widget requires:

- `contact_user` - The user to message (User object)
- `request.user` - The current user (automatically available)

### Configuration

Enable/disable the DM widget in settings:

```python
DJANGO_MESSAGING = {
    "ENABLE_DMS": True,  # Enable DM widget
    "SHOW_DMS_IN_MESSAGES": True,  # Show DMs in main messages page
}
```

### How It Works

The DM widget:

1. Creates a chat between the current user and the contact user (if it doesn't exist)
2. Displays the chat interface inline
3. Allows sending and receiving messages
4. Updates in real-time (with WebSocket) or periodically (with polling)
5. Can be closed and reopened using the toggle button - closing the widget disconnects the real-time connection

### Use Cases

- **User Profiles**: Allow users to message each other from profile pages
- **Team Member Pages**: Enable direct communication with team members
- **Marketplace**: Let buyers message sellers
- **Social Networks**: Facilitate user-to-user communication

### Example: User Directory

```django
{# user_directory.html #}
{% load static %}

<div class="user-directory">
    <h1>Team Members</h1>
    
    {% for member in team_members %}
        <div class="member-card">
            <img src="{{ member.profile.avatar.url }}" alt="{{ member.get_full_name }}">
            <h3>{{ member.get_full_name }}</h3>
            <p>{{ member.profile.title }}</p>
            
            {% if request.user != member %}
                {% include "messaging/includes/dm.html" with contact_user=member %}
            {% endif %}
        </div>
    {% endfor %}
</div>
```

## Room Widget

The room widget creates a chat room attached to any content object (e.g., blog post, project, event).

### Basic Usage

Include the room widget in a detail template:

```django
{# blog_post_detail.html #}
{% load static %}

<article class="blog-post">
    <h1>{{ post.title }}</h1>
    <div class="post-content">
        {{ post.content|safe }}
    </div>
</article>

{% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS %}
    <section class="discussion-section">
        <h2>Discussion</h2>
        {% include "messaging/includes/room.html" with content_object=post %}
    </section>
{% endif %}
```

### Required Context

The room widget requires:

- `content_object` - The object to attach the chat room to (any Django model instance)
- `request.user` - The current user (automatically available)

### Configuration

Enable/disable the room widget in settings:

```python
DJANGO_MESSAGING = {
    "ENABLE_CHAT_ROOMS": True,  # Enable room widget
    "SHOW_CHAT_ROOMS_IN_MESSAGES": True,  # Show rooms in main messages page
}
```

### How It Works

The room widget:

1. Creates a chat room for the content object (if it doesn't exist)
2. Automatically adds the current user as a member
3. Displays the chat interface inline
4. Allows all members to send and receive messages
5. Updates in real-time (with WebSocket) or periodically (with polling)

### Use Cases

- **Blog Posts**: Discussion section for blog posts
- **Projects**: Team communication for projects
- **Events**: Chat room for event attendees
- **Products**: Q&A section for products
- **Courses**: Discussion forum for course lessons

### Example: Project Detail

```django
{# project_detail.html #}
{% load static %}

<div class="project-detail">
    <h1>{{ project.name }}</h1>
    <p>{{ project.description }}</p>
    
    <div class="project-team">
        <h2>Team Members</h2>
        <ul>
            {% for member in project.members.all %}
                <li>{{ member.get_full_name }}</li>
            {% endfor %}
        </ul>
    </div>
    
    {% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS %}
        <div class="project-chat">
            <h2>Team Chat</h2>
            {% include "messaging/includes/room.html" with content_object=project %}
        </div>
    {% endif %}
</div>
```

### Example: Event Page

```django
{# event_detail.html #}
{% load static %}

<div class="event-detail">
    <h1>{{ event.title }}</h1>
    <p><strong>Date:</strong> {{ event.date }}</p>
    <p><strong>Location:</strong> {{ event.location }}</p>
    <div class="event-description">
        {{ event.description|safe }}
    </div>
    
    {% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS and user.is_authenticated %}
        <div class="event-chat">
            <h2>Event Chat</h2>
            <p>Connect with other attendees</p>
            {% include "messaging/includes/room.html" with content_object=event %}
        </div>
    {% endif %}
</div>
```

## Advanced Usage

### Custom Widget Styling

Wrap widgets in custom containers for styling:

```django
<div class="custom-widget-container">
    <div class="widget-header">
        <h3>Send a Message</h3>
    </div>
    <div class="widget-body">
        {% include "messaging/includes/dm.html" with contact_user=profile_user %}
    </div>
</div>
```

```css
.custom-widget-container {
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 1rem;
    background: #f9f9f9;
}

.widget-header {
    margin-bottom: 1rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid #ddd;
}
```

### Conditional Display

Show widgets based on permissions:

```django
{# Only show to authenticated users #}
{% if request.user.is_authenticated %}
    {% include "messaging/includes/dm.html" with contact_user=profile_user %}
{% endif %}

{# Only show to team members #}
{% if request.user in project.members.all %}
    {% include "messaging/includes/room.html" with content_object=project %}
{% endif %}

{# Only show to premium users #}
{% if request.user.profile.is_premium %}
    {% include "messaging/includes/dm.html" with contact_user=support_user %}
{% endif %}
```

### Multiple Widgets on One Page

You can include multiple widgets on the same page:

```django
<div class="contact-page">
    <h1>Contact Us</h1>
    
    <div class="contact-options">
        <div class="contact-option">
            <h2>Sales</h2>
            {% include "messaging/includes/dm.html" with contact_user=sales_user %}
        </div>
        
        <div class="contact-option">
            <h2>Support</h2>
            {% include "messaging/includes/dm.html" with contact_user=support_user %}
        </div>
        
        <div class="contact-option">
            <h2>General Inquiries</h2>
            {% include "messaging/includes/dm.html" with contact_user=info_user %}
        </div>
    </div>
</div>
```

## Customizing Widget Templates

Override widget templates for complete customization:

```
myproject/
    templates/
        messaging/
            includes/
                dm.html          # Custom DM widget
                room.html        # Custom room widget
```

Example custom DM widget:

```django
{# templates/messaging/includes/dm.html #}
<div class="my-custom-dm-widget">
    <div class="dm-header">
        <h3>Message {{ contact_user.get_full_name }}</h3>
    </div>
    <div class="dm-messages">
        {# Your custom message list #}
    </div>
    <div class="dm-input">
        {# Your custom input form #}
    </div>
</div>
```

## Best Practices

### Performance

- **Lazy Loading**: Widgets load messages on demand
- **Pagination**: Only recent messages are loaded initially
- **Caching**: Consider caching user lists and chat metadata

### User Experience

- **Clear Labels**: Make it clear what the widget does
- **Responsive Design**: Ensure widgets work on mobile devices
- **Loading States**: Show loading indicators while data loads

### Security

- **Authentication**: Always check if user is authenticated
- **Permissions**: Verify user has permission to access the chat
- **Validation**: Validate all user input

### Accessibility

- **Keyboard Navigation**: Ensure widgets are keyboard accessible
- **Screen Readers**: Use proper ARIA labels
- **Focus Management**: Manage focus appropriately

## Troubleshooting

### Widget Not Appearing

**Problem**: Widget doesn't show up on the page.

**Solutions**:

1. Check that the feature is enabled in settings
2. Verify the user is authenticated
3. Check template syntax and variable names
4. Look for JavaScript errors in browser console

### Messages Not Loading

**Problem**: Widget appears but messages don't load.

**Solutions**:

1. Check browser console for API errors
2. Verify user has permission to access the chat
3. Check that the content object exists
4. Verify API endpoints are accessible

### Styling Issues

**Problem**: Widget doesn't match your site's design.

**Solutions**:

1. Override Tailwind classes or add custom CSS
2. Add custom CSS classes to widget elements
3. Override widget templates
4. Check for CSS conflicts with your existing styles

## Next Steps

- Learn about [Customization](customization.md) for styling widgets
- Explore the [API Reference](../reference/api.md) for programmatic access
- Check [Template Tags](../reference/template-tags.md) for available helpers
- Review [Settings Reference](../reference/settings.md) for configuration options

