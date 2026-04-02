# Claude Code — macOS Notifications & Terminal Color Hooks

Configured in `~/.claude/settings.json`. Requires **iTerm2** and **terminal-notifier** (`brew install terminal-notifier`).

## How it works

| Event | Sound | macOS Notification | Terminal Background |
|---|---|---|---|
| Claude finishes (`Stop`) | Funk.aiff | "Claude has finished" | Turns green (`#143d14`) |
| Claude needs input (`Notification`) | Funk.aiff | "Claude needs your input" | — |
| User submits a message (`UserPromptSubmit`) | — | — | Resets to default |
| Claude uses a tool (`PreToolUse`) | — | — | Resets to default (fallback) |

## Hook commands

### Stop — play sound + notify + turn background green
```bash
afplay /System/Library/Sounds/Funk.aiff & \
terminal-notifier -message 'Claude has finished' \
  -title "🤖 Claude [${ITERM_SESSION_ID%%:*}]" \
  -activate com.googlecode.iterm2; \
printf '\033]11;#143d14\007' > /dev/tty
```

### Notification — play sound + notify
```bash
afplay /System/Library/Sounds/Funk.aiff & \
terminal-notifier -message 'Claude needs your input' \
  -title "🤖 Claude [${ITERM_SESSION_ID%%:*}]" \
  -activate com.googlecode.iterm2
```

### UserPromptSubmit — reset background to default
```bash
printf '\033]111\007' > /dev/tty
```

### PreToolUse — reset background to default (fallback for tool-using turns)
```bash
printf '\033]111\007' > /dev/tty
```

## Escape sequences

- `\033]11;<hex>\007` — OSC 11: set terminal background color
- `\033]111\007` — OSC 111: reset terminal background to default
- Must write to `/dev/tty` directly since Claude Code captures hook stdout

## SessionStart — set iTerm2 badge to session ID
```bash
printf '\033]1337;SetBadgeFormat=%s\007' \
  $(echo -n "${ITERM_SESSION_ID%%:*}" | base64)
```
