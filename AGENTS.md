# AGENTS.md - OpenCode Configuration Repository

This repository contains personal configuration for OpenCode. It is NOT a software project with build/test/lint steps - it is a collection of markdown and JSON files that configure OpenCode agents, commands, and skills.

## Repository Structure

```
.
├── opencode.json      # Main OpenCode configuration (theme, providers, MCP servers)
├── agents/            # Custom subagent definitions (.md files)
├── commands/          # Slash command definitions (.md files)
└── skills/            # Loadable skill modules (SKILL.md files)
```

## File Formats

### Agent Definitions (`agents/*.md`)

#### Frontmatter Fields

| Field         | Type    | Required | Description                                              |
| ------------- | ------- | -------- | -------------------------------------------------------- |
| `description` | string  | Yes      | When to use this agent (shown in @ autocomplete)         |
| `mode`        | string  | No       | `primary`, `subagent`, or `all` (default: `all`)         |
| `model`       | string  | No       | Override model (format: `provider/model-id`)             |
| `temperature` | number  | No       | 0.0-1.0, controls randomness (default: model-specific)   |
| `tools`       | object  | No       | Enable/disable specific tools (see Available Tools)      |
| `disable`     | boolean | No       | Set to `true` to disable the agent                       |
| `hidden`      | boolean | No       | Hide subagent from @ autocomplete (subagents only)       |

#### Agent Modes

| Mode       | Description                                            |
| ---------- | ------------------------------------------------------ |
| `primary`  | Main agents you interact with directly (Tab to cycle)  |
| `subagent` | Specialized agents invoked via @ mention or Task tool  |
| `all`      | Default if not specified; has full capabilities        |

#### Temperature Guidelines

| Range   | Use Case                                                   |
| ------- | ---------------------------------------------------------- |
| 0.0-0.2 | Very focused and deterministic; code analysis, planning    |
| 0.3-0.5 | Balanced with some creativity; general development         |
| 0.6-1.0 | More creative and varied; brainstorming                    |

#### Tool Configuration

Tools are enabled/disabled with boolean values. Wildcards supported for MCP servers:

```yaml
tools:
  write: false
  edit: false
  bash: false
  mymcp_*: false  # Disable all tools from an MCP server
```

### Command Definitions (`commands/*.md`)

#### Frontmatter Fields

| Field         | Type    | Required | Description                                      |
| ------------- | ------- | -------- | ------------------------------------------------ |
| `description` | string  | Yes      | Brief description shown in command autocomplete  |
| `agent`       | string  | No       | Which agent executes the command                 |
| `model`       | string  | No       | Override the default model                       |

The markdown file name becomes the command name (e.g., `my-command.md` → `/my-command`).

#### Prompt Placeholders

| Placeholder      | Description                              |
| ---------------- | ---------------------------------------- |
| `$ARGUMENTS`     | All arguments passed to the command      |
| `$1`, `$2`, ...  | Positional arguments                     |
| `` !`command` `` | Shell command output injection           |
| `@path/to/file`  | File content inclusion                   |

#### Example Command

```markdown
---
description: Review recent changes
---
Recent git commits:
!`git log --oneline -10`

Review these changes for $1 (or general quality if not specified).
```

### Skill Definitions (`skills/*/SKILL.md`)

#### Frontmatter Fields

| Field           | Type   | Required | Description                                         |
| --------------- | ------ | -------- | --------------------------------------------------- |
| `name`          | string | Yes      | Skill identifier (must match directory name)        |
| `description`   | string | Yes      | 1-1024 characters; helps agent decide when to load  |
| `metadata`      | object | No       | String-to-string map for additional info            |

#### Name Validation Rules

- 1-64 characters
- Lowercase alphanumeric with single hyphen separators
- Cannot start or end with `-`
- Cannot contain consecutive `--`
- Must match the directory name

Body contains detailed instructions that get injected into context when the skill is loaded.

---

## Writing Guidelines

### Markdown Conventions

- Use ATX-style headers (`#`, `##`, `###`)
- Use fenced code blocks with language identifiers
- Use tables for structured data (frontmatter fields, severity levels)
- Prefer bullet lists over numbered lists for non-sequential items
- Keep lines under 100 characters when practical

### Agent Instructions

When writing agent instructions:

1. **Be explicit about scope**: Clearly state what the agent DOES and does NOT do
2. **Define workflows step-by-step**: Use numbered steps for sequential processes
3. **Include output formats**: Show expected response structure with examples
4. **Specify tool usage**: Document which tools to use and how
5. **Add guardrails**: Include "What NOT to Do" sections

### Tone and Style

- Direct and matter-of-fact
- No unnecessary flattery or hedging
- Technical precision over conversational warmth
- Imperative mood for instructions
- Bullet points for scannable content

---

## Configuration Conventions

### opencode.json

- Use `$schema` for validation
- MCP servers use `type: "local"` for CLI commands, `type: "remote"` for URLs
- Disabled servers use `"enabled": false`
- Environment variables go in `environment` object

### Naming Conventions

| Item              | Convention             | Example                       |
| ----------------- | ---------------------- | ----------------------------- |
| Agent files       | lowercase, hyphenated  | `codebase-explorer.md`        |
| Command files     | lowercase, hyphenated  | `gather-context.md`           |
| Skill directories | lowercase, hyphenated  | `code-review/`                |
| Skill files       | `SKILL.md` (uppercase) | `skills/code-review/SKILL.md` |

---

## Code Review Standards

When reviewing changes to this repository, apply the code-review skill methodology:

### Severity Levels

| Severity | Criteria                                                   |
| -------- | ---------------------------------------------------------- |
| Critical | Instructions that could cause data loss or security issues |
| Major    | Incorrect tool configurations, broken workflows            |
| Minor    | Unclear instructions, missing edge cases                   |
| Nitpick  | Style preferences, formatting                              |

### Review Checklist

- [ ] Frontmatter is valid YAML with required fields
- [ ] Instructions are clear and unambiguous
- [ ] Tool configurations match the agent's intended scope
- [ ] Workflows are complete (no missing steps)
- [ ] Output formats are documented
- [ ] Guardrails prevent unintended behavior

---

## Git Commits

Use conventional commit format with atomic, logical commits.

### Commit Message Format

```
type: description
```

Write in imperative mood, focusing on **why** not **what**. Keep the first line under 72 characters.

### Commit Types

| Type        | Purpose                                    | In Changelog |
| ----------- | ------------------------------------------ | ------------ |
| `feat:`     | New user-facing feature                    | Yes          |
| `fix:`      | Bug fix (user-facing)                      | Yes          |
| `perf:`     | Performance improvement                    | Yes          |
| `refactor:` | Code restructuring without behavior change | Yes          |
| `test:`     | Adding or updating tests                   | Yes          |
| `docs:`     | Documentation only                         | No           |
| `chore:`    | Maintenance, tooling, dependencies         | No           |
| `ci:`       | CI/CD changes                              | No           |

### Commit Process

1. **Analyze** - Run `git status -s` and `git diff` to understand changes
2. **Plan** - Group related files into logical commits, draft messages
3. **Confirm** - Present plan to user before executing
4. **Execute** - Use `git add` with specific files (never `-A` or `.`), create commits

### Best Practices

- One logical change per commit
- Never use `git add -A` or `git add .` - always specify files
- Group related changes together
- Split unrelated changes into separate commits
- Message describes the reason for the change, not the change itself

---

## Available Tools

Built-in tools that can be enabled/disabled in agent definitions:

| Tool        | Description                                       |
| ----------- | ------------------------------------------------- |
| `bash`      | Execute shell commands                            |
| `read`      | Read file contents                                |
| `edit`      | Modify existing files using string replacement    |
| `write`     | Create new files or overwrite existing ones       |
| `patch`     | Apply patches to files                            |
| `grep`      | Search file contents using regex                  |
| `glob`      | Find files by pattern matching                    |
| `list`      | List files and directories                        |
| `skill`     | Load a SKILL.md file                              |
| `task`      | Spawn subagents                                   |
| `todowrite` | Manage todo lists (disabled for subagents)        |
| `todoread`  | Read existing todo lists (disabled for subagents) |
| `webfetch`  | Fetch web content                                 |
| `websearch` | Search the web (requires OpenCode provider)       |
| `question`  | Ask the user questions during execution           |

---

## Available Components

### Agents

| Agent               | Purpose                                 | Mode     |
| ------------------- | --------------------------------------- | -------- |
| `codebase-explorer` | Find files, analyze implementations     | subagent |
| `pr-feedback`       | Address GitHub PR review feedback       | all      |
| `prompt-generator`  | Convert plans to implementation prompts | all      |
| `researcher`        | Fetch and analyze web content           | subagent |
| `reviewer`          | Code review with 4-layer methodology    | subagent |
| `tester`            | Write comprehensive test suites         | subagent |

### Commands

| Command           | Purpose                                              |
| ----------------- | ---------------------------------------------------- |
| `/commit`         | Create atomic git commits with conventional messages |
| `/gather-context` | Understand repository state and recent changes       |
| `/research`       | Research using codebase analysis and external docs   |
| `/review`         | Run code review on files or changes                  |
| `/test`           | Write tests using TDD or verification mode           |

### Skills

| Skill                 | Purpose                                                                 |
| --------------------- | ----------------------------------------------------------------------- |
| `code-review`         | 4-layer review methodology (Correctness, Security, Performance, Style)  |
| `frontend-design`     | Create distinctive, production-grade frontend interfaces                |
| `jira-ticket-context` | Load Jira ticket context from git branch                                |
| `testing`             | Testing methodology with coverage priorities and test design principles |

---

## MCP Server Configuration

Current active servers in `opencode.json`:

| Server                | Type           | Purpose                          |
| --------------------- | -------------- | -------------------------------- |
| `chrome-devtools`     | local          | Browser automation and debugging |
| `context7`            | remote         | Documentation lookup             |
| `fetch`               | local (Docker) | Web content fetching             |
| `sequential-thinking` | local          | Step-by-step reasoning           |
| `mongo`               | local          | MongoDB operations (read-only)   |

Disabled servers: `github`, `playwright`

---

## Best Practices

### Adding New Agents

1. Create `agents/<name>.md` with required frontmatter
2. Define clear scope (what it does and doesn't do)
3. Specify tool permissions explicitly
4. Include step-by-step workflow
5. Document output format
6. Add "What NOT to Do" guardrails

### Adding New Commands

1. Create `commands/<name>.md` with description
2. Use `$ARGUMENTS` for user input
3. Define clear workflow steps
4. Specify which agents to spawn (if any)
5. Include tips for efficient execution

### Adding New Skills

1. Create `skills/<name>/SKILL.md`
2. Include frontmatter with name and description
3. Document when to use the skill
4. Provide detailed methodology
5. Include output format examples
6. Add adherence checklist

---

## Error Handling

When instructions fail or produce unexpected results:

1. Check frontmatter syntax (valid YAML)
2. Verify tool permissions match intended scope
3. Ensure workflows are complete and unambiguous
4. Test with explicit edge cases
5. Add guardrails for failure modes
