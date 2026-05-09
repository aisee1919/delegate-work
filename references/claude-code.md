# Claude Code Invocation In Codex Desktop

Prefer non-interactive calls:

```powershell
claude -p "<prompt>"
```

Codex desktop's normal shell may fail to resolve or run npm global shims even when Claude Code works on the machine. Command-not-found, access-denied, or PowerShell execution-policy errors in the normal shell do not prove Claude Code is unavailable.

Retry through an approved/escalated shell command:

```powershell
claude -p "<prompt>"
```

If PATH resolution still fails, call the installed executable directly:

```powershell
& "$env:APPDATA\npm\node_modules\@anthropic-ai\claude-code\bin\claude.exe" -p "<prompt>"
```

Use `claude --version` or the direct executable with `--version` only to verify availability. Do not install, reinstall, or debug npm unless both the escalated `claude` command and the direct executable path fail.

Never include API keys, tokens, passwords, private personal data, or sensitive credentials in helper prompts, logs, skill content, or final answers.
