# Contributing to Zentrix Docs

Thank you for taking the time to contribute! 🎉

## How to Contribute

### Reporting Issues

- Use the [GitHub Issues](https://github.com/zentrixgame-com/zentrix-docs/issues) tracker.
- Search for existing issues before opening a new one.
- Include as much detail as possible: steps to reproduce, expected vs actual behaviour, screenshots if applicable.

### Suggesting Enhancements

- Open an issue with the label `enhancement`.
- Describe the improvement and its benefits clearly.

### Submitting Documentation Changes

1. **Fork** the repository.
2. **Create a branch** from `main`:
   ```bash
   git checkout -b docs/your-change-description
   ```
3. **Make your changes** — follow the writing style guide below.
4. **Commit** with a clear message:
   ```bash
   git commit -m "docs: describe what you changed"
   ```
5. **Open a Pull Request** against `main`.

---

## Writing Style Guide

- Use clear, concise language.
- Write in the **present tense** ("Returns a list of games", not "Will return…").
- Use American English spelling.
- Prefer short paragraphs and bullet points over walls of text.
- All code samples must be wrapped in fenced code blocks with the appropriate language tag (e.g. ` ```json `).
- Every new page must start with a `# Title` heading.
- Add a brief intro paragraph under the title before jumping into sub-sections.

---

## File Naming Convention

| Type | Convention | Example |
|---|---|---|
| Documentation page | `Title-Case-With-Hyphens.md` | `Game-Integration-Guide.md` |
| API spec | `openapi.yaml` | — |
| Template files | `SCREAMING_SNAKE.md` | `CONTRIBUTING.md` |

---

## Code of Conduct

Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md).

---

## Questions?

Open a [Discussion](https://github.com/zentrixgame-com/zentrix-docs/discussions) or reach out at **docs@zentrixgame.com**.
