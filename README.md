# OpenCode Configuration

Personal configuration for [OpenCode](https://opencode.ai), an AI-powered coding assistant.

## Structure

```
.
├── opencode.json      # Main configuration
├── agents/            # Custom agent definitions
├── commands/          # Slash command definitions
└── skills/            # Loadable skill modules
```

## Configuration

`opencode.json` contains:

- **Theme**: `ayu`
- **MCP Servers**:
  - `chrome-devtools` - Browser automation and debugging
  - `context7` - Documentation lookup (remote)
  - `fetch` - Web content fetching (Docker)
  - `sequential-thinking` - Step-by-step reasoning
  - `mongo` - MongoDB operations (read-only, localhost)
  - `github` - GitHub integration (disabled)
  - `playwright` - Browser automation (disabled)

## Commands

Slash commands available via `/command`:

| Command           | Description                                                 |
| ----------------- | ----------------------------------------------------------- |
| `/commit`         | Create atomic git commits with conventional commit messages |
| `/gather-context` | Gather repository state and recent changes                  |
| `/research`       | Research topics using codebase analysis and external docs   |
| `/review`         | Run code review on files or recent changes                  |
| `/test`           | Write tests for code using TDD or verification mode         |

## Agents

Custom subagents for specialized tasks:

| Agent               | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| `codebase-explorer` | Finds files, locates code patterns, and analyzes implementations        |
| `pr-feedback`       | Addresses feedback from GitHub pull request reviews                     |
| `prompt-generator`  | Transforms plans into sequential, self-contained implementation prompts |
| `researcher`        | Fetches and analyzes web content for documentation and best practices   |
| `reviewer`          | Reviews code for correctness, maintainability, and best practices       |
| `tester`            | Writes comprehensive test suites in TDD or verification mode            |

## Skills

Loadable instruction modules that provide domain-specific workflows:

| Skill                 | Description                                                     |
| --------------------- | --------------------------------------------------------------- |
| `code-review`         | 4-layer review methodology                                      |
| `frontend-design`     | Guidelines for creating distinctive frontend interfaces         |
| `jira-ticket-context` | Load Jira ticket context from the current git branch using acli |
| `testing`             | Comprehensive testing methodology                               |
