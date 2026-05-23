# pi-goal-hermes

Goal-driven autonomous continuation extension for [Pi](https://pi.dev). Set a goal and let the agent work autonomously until done, evaluated by a secondary LLM judge.

## Background

This extension is inspired by and ported from [Hermes](https://github.com/anthropics/hermes)'s goal continuation architecture. The core idea -- an LLM judge loop that evaluates progress and decides whether to continue or stop -- is adapted from Hermes's goal system and re-implemented as a native Pi extension using Pi's extension API (custom entries, message renderers, slash commands, and the agent lifecycle hooks).

## Install

```bash
pi install npm:@ricoyudog/pi-goal-hermes
```

Or project-local:

```bash
pi install -l npm:@ricoyudog/pi-goal-hermes
```

## How It Works

1. You set a goal via `/goal <description>`
2. The agent works toward the goal autonomously
3. After each turn, a secondary LLM (judge) evaluates whether the goal is achieved
4. If not done, the agent automatically continues with a follow-up prompt
5. The loop stops when: goal achieved, turn budget exhausted, or manually paused

## Commands

### `/goal`

| Command | Description |
|---------|-------------|
| `/goal <text>` | Set a new goal (starts autonomous loop) |
| `/goal status` | Show current goal status and progress |
| `/goal pause` | Pause the goal loop |
| `/goal stop` | Stop the goal loop (alias for pause) |
| `/goal resume` | Resume a paused goal (resets turn counter) |
| `/goal done` | Manually mark goal as achieved |
| `/goal clear` | Clear the goal entirely |

### `/subgoal`

Add additional acceptance criteria that the judge evaluates alongside the main goal.

| Command | Description |
|---------|-------------|
| `/subgoal <text>` | Add a subgoal criterion |
| `/subgoal list` | List all subgoals |
| `/subgoal remove <n>` | Remove subgoal by index |
| `/subgoal clear` | Clear all subgoals |

## Status Bar

The extension shows goal status in the Pi footer:
- `●` Pursuing goal (active)
- `⏸` Goal paused
- `✓` Goal achieved

## Configuration

- **Default max turns**: 20 (the agent gets 20 turns before the goal auto-pauses with "budget exhausted")
- Turn counter resets on `/goal resume`

## Pause Conditions

The goal automatically pauses when:
- Turn budget exhausted (20 turns by default)
- Judge output unparseable 3 times in a row
- Agent response errors or aborts
- Session reloads
- User interrupts (Ctrl+C)

## Architecture

- **Judge**: Uses a fast model (e.g., Haiku) as a secondary evaluator
- **State persistence**: Goal state is stored in the session via custom entries (survives compaction)
- **Continuation**: Sends follow-up prompts to keep the agent working
- **Event rendering**: Custom message renderers for goal events in the conversation

## License

MIT
