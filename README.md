# Django Messaging Documentation

This repository contains the documentation for Django Messaging, built with MkDocs and the Material theme.

## Documentation Structure

```
src/                          # Documentation source files
├── index.md                  # Home page
├── getting-started/          # Getting started guides
│   ├── installation.md       # Installation guide
│   ├── quick-start.md        # Quick start tutorial
│   └── configuration.md      # Configuration options
├── guides/                   # User guides
│   ├── websocket-setup.md    # WebSocket setup guide
│   ├── polling-setup.md      # Polling setup guide
│   ├── customization.md      # Customization guide
│   └── embedding-widgets.md  # Embedding widgets guide
├── reference/                # Technical reference
│   ├── api.md                # API endpoints reference
│   ├── settings.md           # Settings reference
│   ├── template-tags.md      # Template tags reference
│   └── models.md             # Models reference
├── faq.md                    # Frequently asked questions
├── changelog.md              # Version history
├── contributing.md           # Contributing guidelines
└── license.md                # License information

docs/                         # Built HTML documentation (generated)
```

## Prerequisites

- Python 3.8 or higher
- pip

## Installation

Install MkDocs and the Material theme:

```bash
pip install mkdocs mkdocs-material
```

Or install from requirements if available:

```bash
pip install -r requirements.txt
```

## Building the Documentation

Build the static HTML documentation:

```bash
mkdocs build
```

This will generate the documentation in the `docs/` directory.

## Serving Locally

Serve the documentation locally with live reload:

```bash
mkdocs serve
```

Then visit http://127.0.0.1:8000

The documentation will automatically reload when you make changes to the source files.

## Deploying to GitHub Pages

Deploy the documentation to GitHub Pages:

```bash
mkdocs gh-deploy
```

This will build the documentation and push it to the `gh-pages` branch.

## Configuration

The documentation is configured in `mkdocs.yml`:

- **Site information**: Name, URL, description, author
- **Theme**: Material theme with dark mode support
- **Navigation**: Organized menu structure
- **Markdown extensions**: Code highlighting, admonitions, tabs, etc.

## Writing Documentation

### Markdown Format

Documentation is written in Markdown with additional features:

#### Code Blocks

````markdown
```python
def hello():
    print("Hello, world!")
```
````

#### Admonitions

```markdown
!!! note
    This is a note.

!!! warning
    This is a warning.

!!! tip
    This is a tip.
```

#### Tabs

```markdown
=== "Tab 1"
    Content for tab 1

=== "Tab 2"
    Content for tab 2
```

### Style Guide

- Use clear, concise language
- Include code examples
- Break down complex tasks into steps
- Link to related documentation
- Use proper heading hierarchy (H1 → H2 → H3)

## Project Information

- **Site**: https://websightful.github.io/django-messaging-docs/
- **Author**: Aidas Bendoraitis (DjangoTricks)
- **License**: MIT

## Support

For questions or issues with the documentation, contact Aidas Bendoraitis at https://www.djangotricks.com/feedback/.
