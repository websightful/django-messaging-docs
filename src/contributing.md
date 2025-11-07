# Contributing

Thank you for your interest in contributing to Django Messaging! This guide will help you understand how you can contribute to the project.

## Ways to Contribute

### Report Bugs

If you find a bug, please report it by contacting Aidas Bendoraitis at <https://www.djangotricks.com/feedback/>.

When reporting bugs, please include:

- **Description**: Clear description of the issue
- **Steps to reproduce**: Detailed steps to reproduce the bug
- **Expected behavior**: What you expected to happen
- **Actual behavior**: What actually happened
- **Environment**:
  - Django version
  - Python version
  - Django Messaging version
  - Browser (if applicable)
  - Operating system
- **Error messages**: Any error messages or stack traces
- **Screenshots**: If applicable

### Suggest Features

Have an idea for a new feature? We'd love to hear it!

Contact the developer at <https://www.djangotricks.com/feedback/> with:

- **Feature description**: What you'd like to see
- **Use case**: Why this feature would be useful
- **Examples**: Examples of how it would work
- **Alternatives**: Any alternative solutions you've considered

### Improve Documentation

Documentation improvements are always welcome! If you find:

- Typos or grammatical errors
- Unclear explanations
- Missing information
- Outdated content

Please report them or suggest improvements.

### Share Your Experience

Help others by sharing:

- **Blog posts**: Write about your experience using Django Messaging
- **Tutorials**: Create tutorials for specific use cases
- **Examples**: Share example implementations
- **Case studies**: Share how you're using Django Messaging in production

## Development Guidelines

### Code Style

Django Messaging follows these coding standards:

- **PEP 8**: Python code follows PEP 8 style guide
- **Django conventions**: Follows Django's coding style
- **Type hints**: Use type hints where appropriate
- **Docstrings**: All functions and classes should have docstrings

### Testing

When contributing code:

- Write tests for new features
- Ensure existing tests pass
- Test across supported Python and Django versions
- Test in different browsers (for frontend changes)

### Commit Messages

Use clear, descriptive commit messages:

```
Add user avatar customization feature

- Add GET_USER_AVATAR_URL_FUNCTION setting
- Update documentation
- Add tests for avatar customization
```

## Feature Requests

### Priority Features

The following types of features are prioritized:

1. **Security improvements**
2. **Performance optimizations**
3. **Accessibility enhancements**
4. **Internationalization improvements**
5. **New customization options**

### Feature Evaluation

Features are evaluated based on:

- **Usefulness**: How many users would benefit
- **Complexity**: Implementation complexity
- **Maintenance**: Long-term maintenance burden
- **Compatibility**: Backward compatibility
- **Performance**: Impact on performance

## Bug Reports

### Critical Bugs

Report critical bugs immediately:

- Security vulnerabilities
- Data loss issues
- Complete feature failures
- Crashes or errors preventing use

### Non-Critical Bugs

For non-critical bugs:

- UI/UX issues
- Minor display problems
- Edge case errors
- Performance issues

## Documentation Contributions

### Documentation Structure

Documentation is organized as:

```
docs/
├── index.md                    # Home page
├── getting-started/            # Getting started guides
├── guides/                     # User guides
├── reference/                  # API and technical reference
├── faq.md                      # FAQ
├── changelog.md                # Version history
├── contributing.md             # This file
└── license.md                  # License
```

### Writing Style

When writing documentation:

- **Clear and concise**: Use simple, direct language
- **Examples**: Include code examples
- **Step-by-step**: Break down complex tasks
- **Screenshots**: Use screenshots where helpful
- **Links**: Link to related documentation

### Documentation Format

Documentation uses Markdown with MkDocs:

- Use proper heading hierarchy
- Include code blocks with syntax highlighting
- Use admonitions for notes, warnings, tips
- Add links to related pages

## Code of Conduct

### Our Standards

- **Be respectful**: Treat everyone with respect
- **Be constructive**: Provide constructive feedback
- **Be patient**: Remember that everyone is learning
- **Be inclusive**: Welcome people of all backgrounds

### Unacceptable Behavior

- Harassment or discrimination
- Trolling or insulting comments
- Personal or political attacks
- Publishing others' private information

## Getting Help

### Questions

For questions about:

- **Using Django Messaging**: Check the [FAQ](faq.md) or contact support
- **Contributing**: Contact the developer
- **Custom development**: Contact for custom development services

### Contact

- **Email**: Contact via <https://www.djangotricks.com/feedback/>
- **Website**: <https://www.djangotricks.com>

## License

By contributing to Django Messaging, you agree that your contributions will be licensed under the MIT License.

## Recognition

Contributors will be recognized in:

- Release notes
- Documentation credits
- Project README

## Development Setup

If you're working on custom modifications:

### Local Development

1. Install Django Messaging in development mode
2. Set up a test Django project
3. Configure both WebSocket and polling transports
4. Test across different browsers

### Testing

```bash
# Run tests
python manage.py test django_messaging

# Run specific test
python manage.py test django_messaging.tests.test_models

# Run with coverage
coverage run --source='django_messaging' manage.py test django_messaging
coverage report
```

### Building Documentation

```bash
# Install MkDocs
pip install mkdocs mkdocs-material

# Serve documentation locally
cd django-messaging-docs
mkdocs serve

# Build documentation
mkdocs build
```

## Release Process

Releases follow semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

## Roadmap

Future development priorities:

1. **Performance improvements**
2. **Additional customization options**
3. **Enhanced accessibility**
4. **More internationalization**
5. **Additional integrations**

## Thank You!

Thank you for contributing to Django Messaging! Your contributions help make it better for everyone.

## Questions?

If you have any questions about contributing, please contact Aidas Bendoraitis at <https://www.djangotricks.com/feedback/>.

