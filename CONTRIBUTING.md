# Contributing to CORIOLIX Documentation

Thank you for your interest in contributing to CORIOLIX documentation! This guide will help you get started.

## Development Setup

### Prerequisites

- Python 3.8 or higher
- Git
- Text editor (VS Code recommended)

### Local Environment

1. **Clone the repository**
   ```bash
   git clone https://github.com/R-DESC/CORIOLIX_docs.git
   cd CORIOLIX_docs
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Start the development server**
   ```bash
   mkdocs serve
   ```

4. **View the documentation**
   Open http://127.0.0.1:8000 in your browser

## Content Guidelines

### Writing Style

- Use clear, concise language
- Write in active voice when possible
- Use second person ("you") for instructions
- Keep paragraphs short and focused

### Markdown Formatting

- Use proper heading hierarchy (start with `##` in content pages)
- Include code fences with language specification
- Use admonitions for important information:
  ```markdown
  !!! info "Information"
      This is an info admonition.
  
  !!! warning "Warning"
      This is a warning admonition.
  
  !!! tip "Pro Tip"
      This is a tip admonition.
  ```

### Documentation Structure

- Each major feature should have its own page
- Use descriptive filenames (e.g., `sensor_management.md` not `sensors.md`)
- Include cross-references between related topics
- Add navigation entries in `mkdocs.yml`

## Making Changes

### Workflow

1. Create a feature branch
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Make your changes
3. Test locally with `mkdocs serve`
4. Commit your changes
   ```bash
   git add .
   git commit -m "Add: description of your changes"
   ```

5. Push to GitHub
   ```bash
   git push origin feature/your-feature-name
   ```

6. Create a Pull Request

### Commit Messages

Use conventional commit format:
- `Add:` for new content
- `Update:` for modifications to existing content
- `Fix:` for corrections
- `Remove:` for deletions

Example: `Add: sensor onboarding troubleshooting guide`

## Review Process

1. All changes require review before merging
2. Documentation is automatically built and deployed on merge to master
3. Reviewers will check for:
   - Content accuracy
   - Writing quality
   - Proper formatting
   - Working links

## Resources

- [MkDocs Material Documentation](https://squidfunk.github.io/mkdocs-material/)
- [Markdown Guide](https://www.markdownguide.org/)
- [CORIOLIX API Documentation](API_endpoints.md)

## Questions?

If you have questions about contributing, please:
- Open an issue in the repository
- Contact the R-DESC team
- Check existing documentation for similar patterns