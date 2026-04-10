# Contributing to Grounded AI

Thank you for your interest in contributing! This project is experimental and welcomes contributions that improve the core concept.

## How to Contribute

### Reporting Issues

If you find bugs or have suggestions:

1. Check if the issue already exists
2. Open a new issue with:
   - Clear description
   - Steps to reproduce (for bugs)
   - Expected vs actual behavior
   - Your environment (AI assistant, version, etc.)

### Adding Query Classification Patterns

The skill uses regex patterns to classify queries. To add new patterns:

1. Identify the intent type (see `SKILL.md` section on Query Classification)
2. Create regex that matches real user queries
3. Test with at least 10 example queries
4. Submit a PR with:
   - The pattern
   - Example queries it should match
   - Confidence threshold recommendation

### Supporting New Languages

For dependency graph construction:

1. Add AST parser support for the language
2. Define import/require patterns
3. Update `DependencyNode` interface if needed
4. Test with real projects in that language

### Documentation Improvements

- Fix typos and unclear explanations
- Add more real-world examples
- Translate to other languages
- Create tutorials or videos

## Development Setup

```bash
git clone https://github.com/iammalego/grounded-ai.git
cd grounded-ai

# For testing with Claude Code:
ln -s $(pwd)/SKILL.md ~/.config/claude/skills/grounded-ai/SKILL.md

# For testing with OpenCode:
ln -s $(pwd)/SKILL.md ~/.config/opencode/skills/grounded-ai/SKILL.md
```

## Testing Your Changes

1. Install the modified skill
2. Open a project you're familiar with
3. Ask questions that should trigger the skill:
   - "How does the auth system work?"
   - "Why is this function failing?"
   - "Explain the architecture"
4. Verify the AI:
   - Reads the actual code
   - Uses cached structure when available
   - Provides specific file/line citations
   - Shows confidence in answers

## Pull Request Process

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Update documentation if needed
5. Test thoroughly
6. Commit with clear messages
7. Push to your fork
8. Open a Pull Request

## Code Style

- Follow existing Markdown formatting
- Use TypeScript-style pseudocode for algorithms
- Include concrete examples
- Keep sections focused and well-organized

## Recognition

Contributors will be added to the README acknowledgments section.

## Questions?

Open an issue with the "question" label.

Thank you for helping make AI assistants smarter!
