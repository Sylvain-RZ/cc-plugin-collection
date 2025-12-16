---
description: Generate or update CLAUDE.md for the project root or a specific subdirectory
argument-hint: [path]
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash", "AskUserQuestion"]
---

# Generate CLAUDE.md

Create or update a CLAUDE.md file for project context.

## Arguments

- No argument: Generate/update root CLAUDE.md
- Path provided: Generate subdirectory CLAUDE.md (e.g., `src/api`)

## Process

### For Root CLAUDE.md

1. **Check existing file**
   - If exists, read current content
   - Identify sections to preserve vs update

2. **Analyze project** (if new file)
   - Detect stack from package.json, requirements.txt, go.mod, etc.
   - Find key configuration files
   - Identify build/test commands

3. **Gather details** (if interactive)
   - Use AskUserQuestion for project description (if not evident)
   - Key commands to document
   - Conventions to highlight

4. **Generate content**
   ```markdown
   # Project Name

   Brief description.

   ## Stack
   - Detected technologies

   ## Commands
   | Command | Purpose |
   |---------|---------|
   | detected commands |

   ## Architecture
   Brief overview with @imports to docs

   ## Conventions
   Key rules (or reference to .claude/rules/)
   ```

5. **Write file**
   - Location: `./CLAUDE.md` or `./.claude/CLAUDE.md`
   - Use AskUserQuestion for location preference if unclear

### For Subdirectory CLAUDE.md

1. **Validate path**
   - Ensure directory exists
   - Check if CLAUDE.md already exists

2. **Analyze directory**
   - Determine module purpose from file contents
   - Identify patterns specific to this area

3. **Gather details**
   - Use AskUserQuestion for directory purpose
   - Module-specific conventions to document

4. **Generate focused content**
   ```markdown
   # Module Name

   Purpose of this directory.

   ## Structure
   Key subdirectories and their roles

   ## Conventions
   Module-specific patterns

   ## Key Files
   Important files and their purposes
   ```

## Examples

### Root CLAUDE.md
```
/project-instrumentor:claude-md
```
Generates comprehensive project-level context.

### Subdirectory CLAUDE.md
```
/project-instrumentor:claude-md src/api
```
Generates API-specific context in `src/api/CLAUDE.md`.

```
/project-instrumentor:claude-md tests
```
Generates testing-specific context in `tests/CLAUDE.md`.

## Update Mode

When CLAUDE.md exists:
- Read existing content
- Identify outdated sections (old commands, changed structure)
- Propose updates
- Request confirmation before applying changes
- Preserve previously added custom content

## Tips

- Keep root CLAUDE.md under 1000 words
- Use @imports for lengthy documentation
- Subdirectory files should be 200-500 words
- Focus on non-obvious, project-specific information
