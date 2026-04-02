# Claude Code — Terminal Color & Notification Hooks

Configured in `~/.claude/settings.json`.

---

## macOS (iTerm2)

Requires **iTerm2** and **terminal-notifier** (`brew install terminal-notifier`).

### Behavior

| Event | Sound | macOS Notification | Terminal Background |
|---|---|---|---|
| Claude finishes (`Stop`) | Funk.aiff | "Claude has finished" | Turns green (`#143d14`) |
| Claude needs input (`Notification`) | Funk.aiff | "Claude needs your input" | — |
| User submits a message (`UserPromptSubmit`) | — | — | Resets to default |
| Claude uses a tool (`PreToolUse`) | — | — | Resets to default (fallback) |
| Claude Code exits (`SessionEnd`) | — | — | Resets to default |

### Hook commands

#### Stop — play sound + notify + turn background green
```bash
afplay /System/Library/Sounds/Funk.aiff & \
terminal-notifier -message 'Claude has finished' \
  -title "🤖 Claude [${ITERM_SESSION_ID%%:*}]" \
  -activate com.googlecode.iterm2; \
printf '\033]11;#143d14\007' > /dev/tty
```

#### Notification — play sound + notify
```bash
afplay /System/Library/Sounds/Funk.aiff & \
terminal-notifier -message 'Claude needs your input' \
  -title "🤖 Claude [${ITERM_SESSION_ID%%:*}]" \
  -activate com.googlecode.iterm2
```

#### UserPromptSubmit — reset background to default
```bash
printf '\033]111\007' > /dev/tty
```

#### PreToolUse — reset background to default (fallback for tool-using turns)
```bash
printf '\033]111\007' > /dev/tty
```

#### SessionEnd — reset background on exit
```bash
printf '\033]111\007' > /dev/tty
```

#### SessionStart — set iTerm2 badge to session ID
```bash
printf '\033]1337;SetBadgeFormat=%s\007' \
  $(echo -n "${ITERM_SESSION_ID%%:*}" | base64)
```

---

## Linux / Ubuntu

Color changes via OSC 11/111 work on most terminals (GNOME Terminal, Konsole, Tilix, kitty, etc.). `afplay` and `terminal-notifier` are macOS-only and are omitted. The `Notification` hook is also omitted as it has no Linux equivalent here. For desktop notifications, `notify-send` can be added to the Stop hook if desired.

### Behavior

| Event | Terminal Background |
|---|---|
| Claude finishes (`Stop`) | Turns green (`#143d14`) |
| User submits a message (`UserPromptSubmit`) | Resets to default |
| Claude uses a tool (`PreToolUse`) | Resets to default (fallback) |
| Claude Code exits (`SessionEnd`) | Resets to default |

### Hook commands

#### Stop — turn background green
```bash
printf '\033]11;#143d14\007' > /dev/tty
```

#### UserPromptSubmit — reset background to default
```bash
printf '\033]111\007' > /dev/tty
```

#### PreToolUse — reset background to default (fallback for tool-using turns)
```bash
printf '\033]111\007' > /dev/tty
```

#### SessionEnd — reset background on exit
```bash
printf '\033]111\007' > /dev/tty
```

---

## Escape sequences

- `\033]11;<hex>\007` — OSC 11: set terminal background color
- `\033]111\007` — OSC 111: reset terminal background to default
- `\033]1337;SetBadgeFormat=...\007` — iTerm2 proprietary: set badge (macOS/iTerm2 only)
- Must write to `/dev/tty` directly since Claude Code captures hook stdout
