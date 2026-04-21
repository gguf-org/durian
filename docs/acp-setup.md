# Durian Agent — ACP (Agent Client Protocol) Setup Guide

Durian Agent supports the **Agent Client Protocol (ACP)**, allowing it to run as
a coding agent inside your editor. ACP lets your IDE send tasks to Durian, and
Durian responds with file edits, terminal commands, and explanations — all shown
natively in the editor UI.

---

## Prerequisites

- Durian Agent installed and configured (`durian setup` completed)
- An API key / provider set up in `~/.durian/.env` or via `durian login`
- Python 3.11+

Install the ACP extra:

```bash
pip install -e ".[acp]"
```

---

## VS Code Setup

### 1. Install the ACP Client extension

Open VS Code and install **ACP Client** from the marketplace:

- Press `Ctrl+Shift+X` (or `Cmd+Shift+X` on macOS)
- Search for **"ACP Client"**
- Click **Install**

Or install from the command line:

```bash
code --install-extension anysphere.acp-client
```

### 2. Configure settings.json

Open your VS Code settings (`Ctrl+,` → click the `{}` icon for JSON) and add:

```json
{
  "acpClient.agents": [
    {
      "name": "durian",
      "registryDir": "/path/to/durian/acp_registry"
    }
  ]
}
```

Replace `/path/to/durian` with the actual path to your Durian Agent
installation (e.g. `~/.durian/durian`).

Alternatively, if `durian` is on your PATH, the ACP Client can discover it
automatically via the registry directory.

### 3. Restart VS Code

After configuring, restart VS Code. You should see **Durian Agent** appear in
the ACP agent picker in the chat/agent panel.

---

## Zed Setup

Zed has built-in ACP support.

### 1. Configure Zed settings

Open Zed settings (`Cmd+,` on macOS or `Ctrl+,` on Linux) and add to your
`settings.json`:

```json
{
  "agent_servers": {
    "durian": {
      "type": "custom",
      "command": "durian",
      "args": ["acp"],
    },
  },
}
```

### 2. Restart Zed

Durian Agent will appear in the agent panel. Select it and start a conversation.

---

## JetBrains Setup (IntelliJ, PyCharm, WebStorm, etc.)

### 1. Install the ACP plugin

- Open **Settings** → **Plugins** → **Marketplace**
- Search for **"ACP"** or **"Agent Client Protocol"**
- Install and restart the IDE

### 2. Configure the agent

- Open **Settings** → **Tools** → **ACP Agents**
- Click **+** to add a new agent
- Set the registry directory to your `acp_registry/` folder:
  `/path/to/durian/acp_registry`
- Click **OK**

### 3. Use the agent

Open the ACP panel (usually in the right sidebar) and select **Durian Agent**.

---

## What You Will See

Once connected, your editor provides a native interface to Durian Agent:

### Chat Panel
A conversational interface where you can describe tasks, ask questions, and
give instructions. Durian responds with explanations and actions.

### File Diffs
When Durian edits files, you see standard diffs in the editor. You can:
- **Accept** individual changes
- **Reject** changes you don't want
- **Review** the full diff before applying

### Terminal Commands
When Durian needs to run shell commands (builds, tests, installs), the editor
shows them in an integrated terminal. Depending on your settings:
- Commands may run automatically
- Or you may be prompted to **approve** each command

### Approval Flow
For potentially destructive operations, the editor will prompt you for
approval before Durian proceeds. This includes:
- File deletions
- Shell commands
- Git operations

---

## Configuration

Durian Agent under ACP uses the **same configuration** as the CLI:

- **API keys / providers**: `~/.durian/.env`
- **Agent config**: `~/.durian/config.yaml`
- **Skills**: `~/.durian/skills/`
- **Sessions**: `~/.durian/state.db`

You can run `durian setup` to configure providers, or edit `~/.durian/.env`
directly.

### Changing the model

Edit `~/.durian/config.yaml`:

```yaml
model: openrouter/nous/durian-3-llama-3.1-70b
```

Or set the `DURIAN_MODEL` environment variable.

### Toolsets

ACP sessions use the curated `durian-acp` toolset by default. It is designed for editor workflows and intentionally excludes things like messaging delivery, cronjob management, and audio-first UX features.

---

## Troubleshooting

### Agent doesn't appear in the editor

1. **Check the registry path** — make sure the `acp_registry/` directory path
   in your editor settings is correct and contains `agent.json`.
2. **Check `durian` is on PATH** — run `which durian` in a terminal. If not
   found, you may need to activate your virtualenv or add it to PATH.
3. **Restart the editor** after changing settings.

### Agent starts but errors immediately

1. Run `durian doctor` to check your configuration.
2. Check that you have a valid API key: `durian status`
3. Try running `durian acp` directly in a terminal to see error output.

### "Module not found" errors

Make sure you installed the ACP extra:

```bash
pip install -e ".[acp]"
```

### Slow responses

- ACP streams responses, so you should see incremental output. If the agent
  appears stuck, check your network connection and API provider status.
- Some providers have rate limits. Try switching to a different model/provider.

### Permission denied for terminal commands

If the editor blocks terminal commands, check your ACP Client extension
settings for auto-approval or manual-approval preferences.

### Logs

Durian logs are written to stderr when running in ACP mode. Check:
- VS Code: **Output** panel → select **ACP Client** or **Durian Agent**
- Zed: **View** → **Toggle Terminal** and check the process output
- JetBrains: **Event Log** or the ACP tool window

You can also enable verbose logging:

```bash
DURIAN_LOG_LEVEL=DEBUG durian acp
```

---

## Further Reading

- [ACP Specification](https://github.com/anysphere/acp)
- [Durian Agent Documentation](https://github.com/gguf-org/durian)
- Run `durian --help` for all CLI options
