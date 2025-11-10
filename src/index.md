# Django Messaging

A lightweight, extensible Django app for private messaging, built for easy integration.

## Overview

Django Messaging is a comprehensive messaging solution for Django applications that provides real-time private messaging, group chats, and embedded chat widgets. It's designed to be flexible, easy to integrate, and highly customizable.

## Key Features

### Real-time Messaging System
- Supports both **WebSocket** and **polling** transport mechanisms for live message delivery without page refresh
- Messages appear instantly in the recipient's message list
- Read receipts track message read status with visual indicators

### Multiple Chat Types
- **Direct Messages (DMs)**: One-on-one private conversations between users
- **Group Chats**: Multi-user conversations with member management
- **Chat Rooms**: Public or private chat rooms that can be attached to any content object

### Message Interactions
- **Reactions**: Users can react to messages with emojis
- **Edit Messages**: Users can edit their own messages with real-time updates
- **Delete Messages**: Users can delete their own messages
- **Message Search**: Full-text search across messages with highlighted results

### User Management
- **User Search**: Search users by full name, username, or email
- **Member Management**: Add/remove members from group chats with admin controls
- **Role-based Permissions**: Admin/member roles for chat management
- **Customizable Contact List**: Control who users can message

### Embedded Widgets
- **DM Widget**: Embeddable direct message widget for user profiles
- **Room Widget**: Embeddable chat room for any content object
- Reusable chat components using Django `{% include %}` template tags

### Developer-Friendly
- **RESTful API**: Complete API with Django REST Framework for all chat operations
- **Template-based UI**: Modular, reusable Django template components
- **ASGI and WSGI Compatible**: Works with both ASGI servers (WebSocket) and traditional WSGI servers (polling)
- **Lazy Loading**: Efficient message pagination with automatic DOM cleanup
- **Internationalization Ready**: Built with i18n in mind

## Compatibility

| Feature                          | Value                                          |
|----------------------------------|------------------------------------------------|
| Supported modern browsers        | Chrome, Firefox, Safari, Opera, Microsoft Edge |
| Supported Django versions        | 4.2, 5.0, 5.1, 5.2                             |
| Supported Python versions        | 3.8, 3.9, 3.10, 3.11, 3.12, 3.13, 3.14         |
| Multiple widgets                 | Direct messages, Chat rooms                    |
| Responsive and accessible layout | ✔︎                                             |
| Translatable                     | ✔︎                                             |
| Configurable                     | ✔︎                                             |
| Unlimited websites               | ✔︎                                             |
| Latest package version           | 0.19.1                                         |

## Benefits

- **Save Development Time**: Saves at least 2.5 weeks of AI-powered development work
- **Highly Flexible**: All functionality can be extended, overwritten, or replaced
- **Easy Integration**: Simple setup with minimal configuration required
- **Beautiful UI**: Pretty default styling with light and dark modes
- **Accessible**: Optimized for keyboard and mouse accessibility
- **No Vendor Lock-in**: MIT license, no recurring subscriptions, no domain limitations
- **Production Ready**: Used in real-world projects like [PyBazaar](https://www.pybazaar.com) and [Make-Impact.org](https://www.make-impact.org)

## Quick Example

### Standalone Messages Page

Navigate to `/messages/` to access the full messaging interface.

### Embedded DM Widget

Include the DM widget in a user profile template:

```django
{% if MESSAGING_SETTINGS.ENABLE_DMS and request.user != contact_user %}
    {% include "messaging/includes/dm.html" with contact_user=contact_user %}
{% endif %}
```

### Embedded Chat Room

Include a chat room for any content object:

```django
{% if MESSAGING_SETTINGS.ENABLE_CHAT_ROOMS %}
    {% include "messaging/includes/room.html" with content_object=object %}
{% endif %}
```

## Getting Started

Ready to add messaging to your Django project? Check out our [Installation Guide](getting-started/installation.md) to get started in minutes.

Want to see it in action first? Try the [demo project](https://github.com/websightful/django-messaging-demo-project) to explore all features.

## Support

For technical questions, feature requests, or bug reports, please contact Aidas Bendoraitis at <https://www.djangotricks.com/feedback/>.

## License

Django Messaging is released under the MIT License. See [License](license.md) for details.
