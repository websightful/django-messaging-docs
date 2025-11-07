# Models Reference

Django Messaging uses several models to store messaging data. This reference covers the main models and their fields.

## ChatRoom

The `ChatRoom` model represents a conversation between users.

### Fields

#### id
- **Type**: `UUIDField` (Primary Key)
- **Description**: Unique identifier for the chat room

#### title
- **Type**: `CharField(max_length=255, blank=True)`
- **Description**: Optional title for the chat. Auto-generated for DMs.

#### is_group
- **Type**: `BooleanField(default=False)`
- **Description**: Whether this is a group chat

#### is_room
- **Type**: `BooleanField(default=False)`
- **Description**: True for public/semi-public chat rooms, False for private chats

#### content_type
- **Type**: `ForeignKey(ContentType, null=True, blank=True)`
- **Description**: Content type for room chats (links to a model)

#### object_id
- **Type**: `PositiveIntegerField(null=True, blank=True)`
- **Description**: Object ID for room chats

#### content_object
- **Type**: `GenericForeignKey`
- **Description**: The actual object for room chats

#### members
- **Type**: `ManyToManyField(User, through='ChatMembership', related_name='chat_rooms')`
- **Description**: Users who are members of this chat

#### created_at
- **Type**: `DateTimeField(auto_now_add=True)`
- **Description**: When the chat was created

#### updated_at
- **Type**: `DateTimeField(auto_now=True)`
- **Description**: When the chat was last updated

### Methods

#### can_user_join(user)
Check if a user can join this chat room.

```python
if chat_room.can_user_join(request.user):
    # User can join
    pass
```

#### can_user_leave(user)
Check if a user can leave this chat room.

```python
if chat_room.can_user_leave(request.user):
    # User can leave
    pass
```

### Example Usage

```python
from django_messaging.models import ChatRoom

# Create a group chat
chat_room = ChatRoom.objects.create(
    title="Project Team",
    is_group=True
)

# Add members via ChatMembership
from django_messaging.models import ChatMembership
ChatMembership.objects.create(
    chat=chat_room,
    user=user1,
    role=ChatMembership.Role.ADMIN
)
ChatMembership.objects.create(
    chat=chat_room,
    user=user2,
    role=ChatMembership.Role.MEMBER
)
```

## ChatMembership

The `ChatMembership` model represents a user's membership in a chat room.

### Fields

#### id
- **Type**: `UUIDField` (Primary Key)
- **Description**: Unique identifier for the membership

#### user
- **Type**: `ForeignKey(User, on_delete=CASCADE)`
- **Description**: The user who is a member

#### chat
- **Type**: `ForeignKey(ChatRoom, on_delete=CASCADE)`
- **Description**: The chat room

#### joined_at
- **Type**: `DateTimeField(auto_now_add=True)`
- **Description**: When the user joined

#### role
- **Type**: `CharField(max_length=20, choices=Role.choices)`
- **Choices**: `"admin"`, `"member"`
- **Description**: The user's role in the chat

### Unique Constraint

```python
class Meta:
    unique_together = ('user', 'chat')
```

### Example Usage

```python
from django_messaging.models import ChatMembership

# Add a member
membership = ChatMembership.objects.create(
    chat=chat_room,
    user=new_user,
    role=ChatMembership.Role.MEMBER
)

# Check if user is admin
is_admin = ChatMembership.objects.filter(
    chat=chat_room,
    user=request.user,
    role=ChatMembership.Role.ADMIN
).exists()
```

## Message

The `Message` model represents a single message in a chat.

### Fields

#### id
- **Type**: `UUIDField` (Primary Key)
- **Description**: Unique identifier for the message

#### chat
- **Type**: `ForeignKey(ChatRoom, on_delete=CASCADE, related_name='messages')`
- **Description**: The chat this message belongs to

#### sender
- **Type**: `ForeignKey(User, on_delete=CASCADE, related_name='sent_messages')`
- **Description**: The user who sent the message

#### content
- **Type**: `TextField`
- **Description**: The message content

#### file_attachment
- **Type**: `FileField(upload_to='chat_attachments/', blank=True, null=True)`
- **Description**: Optional file attachment

#### created_at
- **Type**: `DateTimeField(auto_now_add=True)`
- **Description**: When the message was created

#### updated_at
- **Type**: `DateTimeField(auto_now=True)`
- **Description**: When the message was last updated

#### is_edited
- **Type**: `BooleanField(default=False)`
- **Description**: Whether the message has been edited

#### is_deleted
- **Type**: `BooleanField(default=False)`
- **Description**: Whether the message has been deleted

### Example Usage

```python
from django_messaging.models import Message

# Create a message
message = Message.objects.create(
    chat=chat_room,
    sender=request.user,
    content="Hello, everyone!"
)

# Get messages in a chat
messages = Message.objects.filter(chat=chat_room).order_by('created_at')
```

## MessageReadReceipt

The `MessageReadReceipt` model tracks read status for messages.

### Fields

#### id
- **Type**: `UUIDField` (Primary Key)
- **Description**: Unique identifier for the read receipt

#### message
- **Type**: `ForeignKey(Message, on_delete=CASCADE, related_name='read_receipts')`
- **Description**: The message this receipt is for

#### user
- **Type**: `ForeignKey(User, on_delete=CASCADE)`
- **Description**: The user who read the message

#### status
- **Type**: `CharField(max_length=20, choices=Status.choices)`
- **Choices**: `"delivered"`, `"seen"`
- **Description**: The read status

#### timestamp
- **Type**: `DateTimeField(auto_now=True)`
- **Description**: When the status was updated

### Unique Constraint

```python
class Meta:
    unique_together = ('message', 'user')
```

### Example Usage

```python
from django_messaging.models import MessageReadReceipt

# Mark message as seen
receipt, created = MessageReadReceipt.objects.update_or_create(
    message=message,
    user=request.user,
    defaults={'status': MessageReadReceipt.Status.SEEN}
)

# Check if user has seen a message
has_seen = MessageReadReceipt.objects.filter(
    message=message,
    user=request.user,
    status=MessageReadReceipt.Status.SEEN
).exists()
```

## MessageReaction

The `MessageReaction` model represents an emoji reaction to a message.

### Fields

#### id
- **Type**: `UUIDField` (Primary Key)
- **Description**: Unique identifier for the reaction

#### message
- **Type**: `ForeignKey(Message, on_delete=CASCADE, related_name='reactions')`
- **Description**: The message this reaction is for

#### user
- **Type**: `ForeignKey(User, on_delete=CASCADE, related_name='message_reactions')`
- **Description**: The user who added the reaction

#### emoji
- **Type**: `CharField(max_length=10)`
- **Description**: The emoji character(s)

#### created_at
- **Type**: `DateTimeField(auto_now_add=True)`
- **Description**: When the reaction was added

### Unique Constraint

Each user can only add one reaction of each emoji type per message.

```python
class Meta:
    unique_together = ('message', 'user', 'emoji')
```

### Example Usage

```python
from django_messaging.models import MessageReaction

# Add a reaction
reaction = MessageReaction.objects.create(
    message=message,
    user=request.user,
    emoji="👍"
)

# Remove a reaction
MessageReaction.objects.filter(
    message=message,
    user=request.user,
    emoji="👍"
).delete()

# Get reactions for a message
reactions = message.reactions.all()

# Group reactions by emoji
from django.db.models import Count
reaction_summary = message.reactions.values('emoji').annotate(
    count=Count('id')
)
```



## Database Schema

### Entity Relationship Diagram

```
User (Django's User model)
  |
  |-- user (FK) --> ChatMembership
  |-- sender (FK) --> Message
  |-- user (FK) --> MessageReadReceipt
  |-- user (FK) --> MessageReaction

ChatRoom
  |-- chat (FK) --> ChatMembership
  |-- chat (FK) --> Message
  |-- content_type (FK) --> ContentType
  |-- object_id --> Any Model (GenericForeignKey)

Message
  |-- message (FK) --> MessageReadReceipt
  |-- message (FK) --> MessageReaction

ChatMembership
  |-- Through table connecting User and ChatRoom
```

## Next Steps

- Check the [API Reference](api.md) for API endpoints
- Review [Template Tags](template-tags.md) for template usage
- Explore [Settings Reference](settings.md) for configuration

