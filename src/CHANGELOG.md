# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [v0.20.0] - 2025-11-11

### Added

- Login URL setting to use for DM and Room widgets.

## [v0.19.2] - 2025-11-11

### Fixed

- Layout issues for non-authenticated visitors fixed.
- Non-authenticated visitors get linked to the login page with a `?next=` parameter to return to the current page after login.

## [v0.19.1] - 2025-11-10

### Fixed

- Room messages show context menu only on hover or focus.

## [v0.19.0] - 2025-11-09

### Changed

- The height of DM widget increased to approximately 75vh.

## [v0.18.1] - 2025-11-09

### Fixed

- Mobile tabs should work well now.
- The mobile view after renaming a chat fixed.

## [v0.18.0] - 2025-11-09

### Changed

- Responsive layout with tabs on mobile for the messages view: chat list and message list.

## [v0.17.0] - 2025-11-01

### Fixed

- Focus states for input fields made theme aware.

## [v0.16.3] - 2025-11-01

### Fixed

- Showing the user name in the DM title from the `display_name` field.

## [v0.16.2] - 2025-11-01

### Fixed

- Making sure that all buttons are using the button.html template.
- Styling corrections.

## [v0.16.1] - 2025-11-01

### Changed

- Code reformatted.

## [v0.16.0] - 2025-10-31

### Fixed

- Translatable strings fixed for DMs.
- Styling corrections.

## [v0.15.0] - 2025-10-30

### Added

- Customizable user name display.
- Customizable user avatar rendering.
- User avatars in chat list and message list.

### Fixed

- Scrolling position when loading older messages fixed.

## [v0.14.4] - 2025-10-30

### Fixed

- User primary key compatibility: Fixed URL pattern for member removal endpoint to support all primary key types (integer, UUID, and string-based).

## [v0.14.3] - 2025-10-30

### Fixed

- Custom user model support: The package now fully supports projects with custom user models configured via `AUTH_USER_MODEL` setting.

## [v0.14.2] - 2025-10-30

### Changed

- README updated with information about Uvicorn.

## [v0.14.1] - 2025-10-30

### Fixed

- Context menu visibility for messages fixed.

## [v0.14.0] - 2025-10-28

### Changed

- New CHANGELOG syntax for the headers.

## [v0.13.0] - 2025-10-28

### Added

- Dark mode added.

## [v0.12.0] - 2025-10-28

### Changed

- The main messages view and its template renamed.

## [v0.11.2] - 2025-10-28

### Fixed

- Styling of message context menus fixed.

## [v0.11.1] - 2025-10-27

### Fixed

- The package cleaned up: no *.pyc files, no __pycache__ directories.

## [v0.11.0] - 2025-10-27

### Added

- Configurable base template.

### Changed

- Default top navigation height set to 0px as for the default base template there is no navigation.

## [v0.10.1] - 2025-10-26

### Fixed

- README updated.

## [v0.10.0] - 2025-10-26

### Fixed

- Consumers shouldn't load models prematurely.
- There should be no hardcoded URLs.

### Removed

- Custom button texts per chat room.

## [v0.9.0] - 2025-10-25

### Added

- __Real-time messaging system__ - Supports both WebSocket and polling transport mechanisms for live message delivery without page refresh.
- __ASGI and WSGI compatible__ - Works with both ASGI servers (for WebSocket support via Django Channels) and traditional WSGI servers (using polling transport).
- __Multiple chat types__ - Provides direct messages (DMs), group chats, and public/private chat rooms with content object integration.
- __Message interactions__ - Users can react to messages with emojis, edit their own messages, and delete messages with real-time updates.
- __Read receipts and typing indicators__ - Tracks message read status with visual indicators and shows when other users are typing.
- __Lazy loading and pagination__ - Implements efficient message segmentation with initial 10 messages, scroll-up to load older messages, and automatic DOM cleanup.
- __User search and member management__ - Search users by full name, username, or email; add/remove members from group chats with admin controls.
- __Embedded chat widgets__ - Reusable chat components that can be embedded in any page, including DM widgets for user profiles and room widgets for content objects.
- __Message search and filtering__ - Full-text search across messages with highlighted results and chat list filtering capabilities.
- __Template-based UI__ - Modular, reusable Django template components using {% include %} tags for buttons, context menus, and chat interfaces.
- __RESTful API with Django REST Framework__ - Complete API for chat operations including message CRUD, reactions, read receipts, and member management.
- __Role-based permissions and access control__ - Admin/member roles for chat management, membership-based message access, ownership validation for edits/deletes, and customizable contact list function for controlling who users can message.

<!--
### Added
### Changed
### Deprecated
### Removed
### Fixed
### Security
-->


