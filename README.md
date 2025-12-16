# CC Plugin Collection

A curated collection of plugins for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Available Plugins

| Plugin | Description |
|--------|-------------|
| [project-instrumentor](./plugins/project-instrumentor/) | Advanced project instrumentation - generate CLAUDE.md, rules, commands, agents, skills, and output styles |

## Installation

### Single Plugin

```bash
claude --plugin-dir ./plugins/plugin-name
```

### From GitHub

```bash
# Clone the marketplace
git clone https://github.com/Sylvain-RZ/cc-plugin-collection.git

# Use a specific plugin
claude --plugin-dir ./cc-plugin-collection/plugins/project-instrumentor
```

## Plugin Structure

Each plugin follows the standard Claude Code plugin structure:

```
plugins/plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest
├── agents/              # Custom agents
├── commands/            # Slash commands
├── skills/              # Knowledge skills
└── README.md            # Plugin documentation
```

## Contributing

Want to add your plugin to the marketplace?

1. Fork this repository
2. Create your plugin in `plugins/your-plugin-name/`
3. Follow the [plugin structure](#plugin-structure)
4. Submit a pull request

### Plugin Requirements

- Must include a valid `plugin.json` manifest
- Must include a `README.md` with usage instructions
- Should follow Claude Code plugin best practices

## License

MIT - Each plugin may have its own license specified in its directory.
