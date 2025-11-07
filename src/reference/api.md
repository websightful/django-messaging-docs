# API Reference

Django Messaging provides a comprehensive REST API built with Django REST Framework. This reference covers all available endpoints.

!!! note "API Response Structure"
    The examples in this document show the general structure of API responses. The actual field names and structure may vary. For the most accurate and up-to-date API schema, refer to the serializers in `django_messaging/serializers.py` or use the Django REST Framework browsable API at `/messages/api/`.

## Base URL

All API endpoints are prefixed with `/messages/api/` (assuming you've included the URLs at `/messages/`).

## Authentication

All API endpoints require authentication. The API uses Django's session authentication by default.

```python
# Example using requests library
import requests

session = requests.Session()
session.post('http://example.com/login/', data={'username': 'user', 'password': 'pass'})
response = session.get('http://example.com/messages/api/chats/')
```

## Chats

### List Chats

Get a list of all chats for the current user.

**Endpoint**: `GET /messages/api/chats/`

**Query Parameters**:

- `page` (optional): Page number for pagination
- `search` (optional): Search query to filter chats

**Response**:

```json
{
    "count": 10,
    "next": "http://example.com/messages/api/chats/?page=2",
    "previous": null,
    "results": [
        {
            "id": 1,
            "title": "John Doe",
            "chat_type": "dm",
            "created_at": "2025-01-01T12:00:00Z",
            "updated_at": "2025-01-02T15:30:00Z",
            "last_message": {
                "id": 42,
                "content": "Hello!",
                "created_at": "2025-01-02T15:30:00Z",
                "sender": {
                    "id": 2,
                    "username": "johndoe",
                    "display_name": "John Doe"
                }
            },
            "unread_count": 3,
            "members": [
                {
                    "id": 1,
                    "username": "currentuser",
                    "display_name": "Current User"
                },
                {
                    "id": 2,
                    "username": "johndoe",
                    "display_name": "John Doe"
                }
            ]
        }
    ]
}
```

### Create Chat

Create a new chat.

**Endpoint**: `POST /messages/api/chats/`

**Request Body**:

```json
{
    "chat_type": "group",
    "title": "Project Team",
    "member_ids": [2, 3, 4]
}
```

**Response**: `201 Created`

```json
{
    "id": 5,
    "title": "Project Team",
    "chat_type": "group",
    "created_at": "2025-01-02T16:00:00Z",
    "members": [...]
}
```

### Get Chat Detail

Get details of a specific chat.

**Endpoint**: `GET /messages/api/chats/{chat_id}/`

**Response**:

```json
{
    "id": 1,
    "title": "John Doe",
    "chat_type": "dm",
    "created_at": "2025-01-01T12:00:00Z",
    "updated_at": "2025-01-02T15:30:00Z",
    "members": [...],
    "admins": [1]
}
```

### Update Chat

Update chat details (title, etc.).

**Endpoint**: `PUT /messages/api/chats/{chat_id}/` or `PATCH /messages/api/chats/{chat_id}/`

**Request Body**:

```json
{
    "title": "New Chat Title"
}
```

**Response**: `200 OK`

### Delete Chat

Delete a chat.

**Endpoint**: `DELETE /messages/api/chats/{chat_id}/`

**Response**: `204 No Content`

## Messages

### List Messages

Get messages for a specific chat.

**Endpoint**: `GET /messages/api/chats/{chat_id}/messages/`

**Query Parameters**:

- `page` (optional): Page number for pagination
- `before` (optional): Get messages before this message ID
- `after` (optional): Get messages after this message ID

**Response**:

```json
{
    "count": 50,
    "next": "http://example.com/messages/api/chats/1/messages/?page=2",
    "previous": null,
    "results": [
        {
            "id": 42,
            "content": "Hello!",
            "created_at": "2025-01-02T15:30:00Z",
            "updated_at": "2025-01-02T15:30:00Z",
            "is_edited": false,
            "is_deleted": false,
            "sender": {
                "id": 2,
                "username": "johndoe",
                "display_name": "John Doe",
                "avatar_url": "https://..."
            },
            "reactions": [
                {
                    "emoji": "👍",
                    "count": 2,
                    "users": [1, 3]
                }
            ],
            "read_by": [1, 2]
        }
    ]
}
```

### Send Message

Send a new message to a chat.

**Endpoint**: `POST /messages/api/chats/{chat_id}/messages/`

**Request Body**:

```json
{
    "content": "Hello, everyone!"
}
```

**Response**: `201 Created`

```json
{
    "id": 43,
    "content": "Hello, everyone!",
    "created_at": "2025-01-02T16:00:00Z",
    "sender": {...}
}
```

### Get Message Detail

Get details of a specific message.

**Endpoint**: `GET /messages/api/messages/{message_id}/`

**Response**:

```json
{
    "id": 42,
    "content": "Hello!",
    "created_at": "2025-01-02T15:30:00Z",
    "sender": {...},
    "chat": 1
}
```

### Update Message

Edit a message (only your own messages).

**Endpoint**: `PUT /messages/api/messages/{message_id}/` or `PATCH /messages/api/messages/{message_id}/`

**Request Body**:

```json
{
    "content": "Updated message content"
}
```

**Response**: `200 OK`

### Delete Message

Delete a message (only your own messages).

**Endpoint**: `DELETE /messages/api/messages/{message_id}/`

**Response**: `204 No Content`

## Reactions

### Add/Remove Reaction

Add or remove an emoji reaction to a message.

**Endpoint**: `POST /messages/api/messages/{message_id}/reactions/`

**Request Body**:

```json
{
    "emoji": "👍"
}
```

**Response**: `200 OK`

```json
{
    "action": "added",
    "emoji": "👍",
    "message_id": 42
}
```

If the reaction already exists, it will be removed:

```json
{
    "action": "removed",
    "emoji": "👍",
    "message_id": 42
}
```

## Read Receipts

### Mark Chat as Read

Mark all messages in a chat as read.

**Endpoint**: `POST /messages/api/chats/{chat_id}/mark-as-read/`

**Response**: `200 OK`

```json
{
    "status": "success",
    "marked_count": 5
}
```

## Members

### Add Members

Add members to a group chat.

**Endpoint**: `POST /messages/api/chats/{chat_id}/members/add/`

**Request Body**:

```json
{
    "user_ids": [5, 6, 7]
}
```

**Response**: `200 OK`

```json
{
    "status": "success",
    "added_count": 3
}
```

### Remove Member

Remove a member from a group chat.

**Endpoint**: `DELETE /messages/api/chats/{chat_id}/members/{user_id}/remove/`

**Response**: `204 No Content`

## Users

### Search Users

Search for users to add to chats.

**Endpoint**: `GET /messages/api/users/search/`

**Query Parameters**:

- `q` (required): Search query (searches name, username, email)

**Response**:

```json
{
    "results": [
        {
            "id": 5,
            "username": "janedoe",
            "display_name": "Jane Doe",
            "email": "jane@example.com",
            "avatar_url": "https://..."
        }
    ]
}
```

## Typing Indicators

### Send Typing Indicator

Indicate that the user is typing.

**Endpoint**: `POST /messages/api/chats/{chat_id}/typing/`

**Request Body**:

```json
{
    "is_typing": true
}
```

**Response**: `200 OK`

## Error Responses

All endpoints may return the following error responses:

### 400 Bad Request

```json
{
    "error": "Invalid request",
    "details": {
        "field_name": ["Error message"]
    }
}
```

### 401 Unauthorized

```json
{
    "detail": "Authentication credentials were not provided."
}
```

### 403 Forbidden

```json
{
    "detail": "You do not have permission to perform this action."
}
```

### 404 Not Found

```json
{
    "detail": "Not found."
}
```

### 500 Internal Server Error

```json
{
    "error": "Internal server error"
}
```

## Rate Limiting

The API does not implement rate limiting by default. Consider adding rate limiting in production using Django REST Framework's throttling classes.

## Pagination

List endpoints use cursor pagination by default. The response includes:

- `count`: Total number of items
- `next`: URL to the next page (null if no more pages)
- `previous`: URL to the previous page (null if first page)
- `results`: Array of items

## WebSocket Events

When using WebSocket transport, the following events are broadcast:

### New Message

```json
{
    "type": "new_message",
    "message": {
        "id": 42,
        "content": "Hello!",
        "sender": {...}
    }
}
```

### Message Updated

```json
{
    "type": "message_updated",
    "message": {
        "id": 42,
        "content": "Updated content",
        "is_edited": true
    }
}
```

### Message Deleted

```json
{
    "type": "message_deleted",
    "message_id": 42
}
```

### Reaction Added/Removed

```json
{
    "type": "reaction_updated",
    "message_id": 42,
    "reactions": [...]
}
```

### Typing Indicator

```json
{
    "type": "typing",
    "user_id": 2,
    "is_typing": true
}
```

## Next Steps

- Check the [Settings Reference](settings.md) for configuration options
- Explore [Template Tags](template-tags.md) for template integration
- Review [Models](models.md) for data structure

