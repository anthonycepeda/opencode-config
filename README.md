# OpenCode Setup

AI-assisted coding in the terminal using [OpenCode](https://opencode.ai) with our internal Qwen3 model.

---

## What's in this repo

| File | Purpose | Where it goes |
|------|---------|---------------|
| `opencode.json` | Global config — provider, model, LSP | `~/.config/opencode/opencode.json` |
| `global-AGENTS.md` | Personal rules applied to every session | `~/.config/opencode/AGENTS.md` |
| `project-AGENTS.md` | Template for per-project rules | Rename to `AGENTS.md`, commit to your project root |

---

## Installation

```bash
curl -fsSL https://opencode.ai/install | bash
```

Then add the binary to your PATH if prompted:

```bash
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Verify:

```bash
opencode --version
```

---

## Configuration

### 1. Create the config directory

```bash
mkdir -p ~/.config/opencode
```

### 2. Copy the global config

```bash
cp opencode.json ~/.config/opencode/opencode.json
```

Open it and fill in the two placeholders:

```json
"baseURL": "https://YOUR_INTERNAL_ENDPOINT/v1",
"apiKey":  "YOUR_API_KEY_OR_ENV_VAR"
```

> If your API key is available as an environment variable (e.g. `API_KEY`), you can set `"apiKey": "${API_KEY}"` and export it in your shell profile instead of hardcoding it.

### 3. Copy the global personal rules

```bash
cp global-AGENTS.md ~/.config/opencode/AGENTS.md
```

This file is never committed to git. It applies your personal context (stack, conventions, specific gotchas) to every OpenCode session.

### 4. Python LSP (optional but recommended)

OpenCode integrates with `pylsp` to provide live diagnostics to the model. Install it once:

```bash
uv tool install python-lsp-server
```

---

## Per-project setup

For each project you want OpenCode to understand deeply, copy the template and fill it in:

```bash
cp project-AGENTS.md ~/dev/your-project/AGENTS.md
```

Then edit `AGENTS.md` to reflect the project's architecture, stack, and conventions. Commit it to git — it's shared with the team and improves every session on that project.

Run this inside any project to let OpenCode generate a first draft automatically:

```bash
opencode
/init
```

> If the project already has a `CLAUDE.md`, OpenCode will use it as a fallback automatically. You don't need to rename it.

---

## Daily usage

```bash
# Start an interactive session in your project directory
cd ~/dev/your-project
opencode

# Switch between Build (full tools) and Plan (read-only) agents
Tab

# Fuzzy-search for a file to include in context
@

# Undo the last set of changes the agent made
/undo
```

---

## Troubleshooting

**`opencode: command not found`**
Add the install directory to your PATH:

```bash
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

**Model not responding / connection errors**
Check that your internal endpoint is reachable and your API key is set. The `baseURL` in `opencode.json` must match what the team has provisioned.

**Artifactory / TLS errors when using uv inside a session**
The correct fix is `allow-insecure-host = ["artifactory.cib.echonet"]` in `pyproject.toml`. Do not use `verify-ssl = false` — it does not suppress `BadSignature` errors.

---

## Updating your rules

The most important habit: **update `AGENTS.md` every time the agent makes a mistake or you establish a new pattern.** The file is a living document, not a one-time setup.

- Mistake in project X → add a rule to `X/AGENTS.md`
- Mistake that applies everywhere → add a rule to `~/.config/opencode/AGENTS.md`
