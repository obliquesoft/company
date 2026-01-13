# Claude Code Team Guidelines

Best practices for using Claude Code as a team, synthesized from industry experts including [Boris Cherny](https://x.com/bcherny/status/2007179832300581177) (creator of Claude Code), [Sankalp's Guide](https://sankalp.bearblog.dev/my-experience-with-claude-code-20-and-how-to-get-better-at-using-coding-agents/), [Ryan Carson's workflow](https://x.com/ryancarson/status/2008548371712135632), [Martin Fowler's observations](https://martinfowler.com/fragments/2026-01-08.html), and others.

## Philosophy

- Claude Code works great out of the box - don't over-customize
- There's no single "correct" way to use it; customize it for your workflow
- Each team member may use it differently, and that's okay
- Learning Claude Code transfers directly to other CLI coding tools

## Model Selection

Use **Opus 4.5 with thinking** for everything. Even though it's bigger and slower than Sonnet, it requires less steering and is better at tool use, making it faster overall for complex tasks.

For specific use cases:
- **Opus 4.5**: Primary model for all coding work
- **Sonnet**: Sub-agents for grep, file reading, and filtering tasks
- **Haiku**: Lightweight sub-agent tasks where speed matters more than depth

## Shared CLAUDE.md

Maintain a single `CLAUDE.md` file in your repository root, checked into git:

- The whole team should contribute to it regularly
- Anytime you see Claude do something incorrectly, add a note to `CLAUDE.md` so it doesn't happen again
- During code review, use `@.claude` on PRs to add learnings
- Keep it focused and useful (aim for ~2.5k tokens or less)
- Document style conventions, design guidelines, and PR templates

### AGENTS.md Format

Consider adopting the [AGENTS.md standard](https://www.aihero.dev/my-agents-md-file-for-building-plans-you-actually-read) - a simple, open format used by 60,000+ open-source projects:

- Cover six core areas: commands, testing, project structure, code style, git workflow, and boundaries
- Keep it under 150 lines - long files slow the agent and bury signal
- Start simple, test it, add detail when your agent makes mistakes
- The best agent files grow through iteration, not upfront planning

## Plan Mode First

Start most sessions in **Plan mode** (`shift+tab` twice):

1. Use Plan mode to go back and forth with Claude until you like its plan
2. Once the plan is solid, switch to auto-accept edits mode
3. With a good plan, Claude can usually 1-shot the implementation

A good plan is really important for quality results.

### Spec-Based Development

[Thariq's approach](https://x.com/trq212/status/2005315275026260309): Start with a minimal spec or prompt and ask Claude to interview you using the `AskUserQuestionTool`, then make a new session to execute the spec.

### Extended Thinking Prompts

Use thinking prompts for complex problems:
- `/think` - standard extended thinking
- `/think hard` - deeper analysis
- `/ultrathink` - maximum reasoning depth

## Parallel Sessions

For maximum productivity, run multiple Claude sessions in parallel:

- Run 5 parallel Claudes in terminal tabs, numbered 1-5
- Use system notifications to know when a Claude needs input
- Run additional sessions (5-10) on claude.ai/code alongside local sessions
- Hand off local sessions to web using `&`
- Use `--teleport` to move sessions between environments
- Keep each session in its own git checkout to avoid conflicts
- Start mobile sessions in the morning and check in on them later

Note: Expect 10-20% of sessions to be abandoned due to unexpected scenarios - this is normal.

## Slash Commands

Create slash commands for frequently used workflows:

- Store them in `.claude/commands/` and check into git
- Share them with your team
- Examples:
  - `/commit-push-pr` - commit, push, and create a PR in one command
  - Create commands for any "inner loop" workflow you do many times a day

### Code Simplifier Plugin

Use the [official code-simplifier agent](https://x.com/bcherny/status/2009450715081789767):

```bash
claude plugin install code-simplifier
```

Or from within a session:
```
/plugin marketplace update claude-plugins-official
/plugin install code-simplifier
```

Use at the end of a long coding session or to clean up complex PRs.

## Subagents

Use subagents to automate common workflows:

- `code-simplifier` - simplify code after Claude finishes
- `verify-app` - run end-to-end tests
- Think of subagents as automated encapsulations of your most common PR workflows
- For tasks like grep, reading, and filtering code, even Haiku is sufficient

Launch multiple agents in parallel:
```
Launch 3 sonnet based agents to...
```

## Verification (Key Tip)

**Give Claude a way to verify its work.** This is probably the most important thing for getting great results:

- If Claude has a feedback loop, it will 2-3x the quality of the final result
- Set up linting, tests, or other verification hooks
- Let Claude run these checks and fix issues before you review
- Claude can test UI changes using the Chrome extension

## Hooks

### PostToolUse Hooks

Run a `PostToolUse` hook to clean up code formatting:

- Claude's code is usually well formatted, but inconsistencies can cause CI failures
- A formatting hook ensures consistent style across all generated code

## Permissions

Rather than using `--dangerously-skip-permissions`:

- Use `/permissions` to pre-allow common bash commands that are safe in your environment
- Check these into `.claude/settings.json` and share with the team
- This balances security with workflow efficiency

## MCP Connectors

[Skills, subagents, and MCP connectors](https://x.com/eyad_khrais/status/2010076957938188661) are capabilities that separate competent usage from exceptional usage. The documentation doesn't always explain how these pieces fit together in practice.

## Browser Control

[Claude Code can control your browser](https://x.com/dabit3/status/2007639328663474672) using your login and session state, accessing anything you're already logged into without API keys or OAuth setup.

Example workflows:
- "Open the PR preview, click through every link..."
- UI testing with real browser interaction
- Automated QA workflows

## Session Management

- Use `/resume` to continue from an old session
- Use `/rewind` to recover safely from mistakes
- Tell Claude to keep documenting changes in a scratchpad for when you start a new session on the same branch

## Context Management

As [Martin Fowler observes](https://martinfowler.com/fragments/2026-01-08.html), "the crucial thing I hear is managing context" from people who are serious about AI-assisted programming.

## Code Review with Multiple Models

For reviewing code and finding bugs, consider using different models for different tasks. Some developers find certain models better at finding bugs and mentioning severity levels (P1, P2) with fewer false positives.

## GitHub Integration

Use the Claude Code GitHub action for PR workflows:

- Run `/install-github-action` to set it up
- Use `@.claude` in PR comments to trigger Claude Code actions
- Automate documentation updates and CLAUDE.md additions through PRs

## Resources

- [Boris Cherny's Setup Thread](https://x.com/bcherny/status/2007179832300581177)
- [Sankalp's Claude Code Guide](https://sankalp.bearblog.dev/my-experience-with-claude-code-20-and-how-to-get-better-at-using-coding-agents/)
- [AGENTS.md for Building Plans](https://www.aihero.dev/my-agents-md-file-for-building-plans-you-actually-read)
- [Ryan Carson's 3-Step Workflow](https://x.com/ryancarson/status/2008548371712135632)
- [Nader Dabit's Browser Control Tips](https://x.com/dabit3/status/2007639328663474672)
- [Martin Fowler on AI Workflows](https://martinfowler.com/fragments/2026-01-08.html)
- [Anthropic's Agent Evals Guide](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [Every.to Agent-Native Guide](https://every.to/guides/agent-native)
