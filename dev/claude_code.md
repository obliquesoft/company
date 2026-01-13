# Claude Code Team Guidelines

Best practices for using Claude Code as a team, based on insights from [Boris Cherny](https://x.com/bcherny/status/2007179832300581177), the creator of Claude Code.

## Philosophy

- Claude Code works great out of the box - don't over-customize
- There's no single "correct" way to use it; customize it for your workflow
- Each team member may use it differently, and that's okay

## Model Selection

Use **Opus 4.5 with thinking** for everything. Even though it's bigger and slower than Sonnet, it requires less steering and is better at tool use, making it faster overall for complex tasks.

## Shared CLAUDE.md

Maintain a single `CLAUDE.md` file in your repository root, checked into git:

- The whole team should contribute to it regularly
- Anytime you see Claude do something incorrectly, add a note to `CLAUDE.md` so it doesn't happen again
- During code review, use `@.claude` to add learnings to CLAUDE.md as part of PRs
- Keep it focused and useful (aim for ~2.5k tokens or less)

## Plan Mode First

Start most sessions in **Plan mode** (`shift+tab` twice):

1. Use Plan mode to go back and forth with Claude until you like its plan
2. Once the plan is solid, switch to auto-accept edits mode
3. With a good plan, Claude can usually 1-shot the implementation

A good plan is really important for quality results.

## Parallel Sessions

For maximum productivity, run multiple Claude sessions in parallel:

- Run 5 parallel Claudes in terminal tabs, numbered 1-5
- Use system notifications to know when a Claude needs input
- Run additional sessions (5-10) on claude.ai/code alongside local sessions
- Hand off local sessions to web using `&`
- Use `--teleport` to move sessions between environments
- Keep each session in its own git checkout to avoid conflicts

Note: Expect 10-20% of sessions to be abandoned due to unexpected scenarios - this is normal.

## Slash Commands

Create slash commands for frequently used workflows:

- Store them in `.claude/commands/` and check into git
- Share them with your team
- Examples:
  - `/commit-push-pr` - commit, push, and create a PR in one command
  - Create commands for any "inner loop" workflow you do many times a day

This saves repetitive prompting and lets Claude use these workflows too.

## Subagents

Use subagents to automate common workflows:

- `code-simplifier` - simplify code after Claude finishes
- `verify-app` - run end-to-end tests
- Think of subagents as automated encapsulations of your most common PR workflows

## Verification (Key Tip)

**Give Claude a way to verify its work.** This is probably the most important thing for getting great results:

- If Claude has a feedback loop, it will 2-3x the quality of the final result
- Set up linting, tests, or other verification hooks
- Let Claude run these checks and fix issues before you review

## PostToolUse Hooks

Run a `PostToolUse` hook to clean up code formatting:

- Claude's code is usually well formatted, but inconsistencies can cause CI failures
- A formatting hook ensures consistent style across all generated code

## Permissions

Rather than using `--dangerously-skip-permissions`:

- Use `/permissions` to pre-allow common bash commands that are safe in your environment
- Check these into `.claude/settings.json` and share with the team
- This balances security with workflow efficiency

## GitHub Integration

Use the Claude Code GitHub action for PR workflows:

- Run `/install-github-action` to set it up
- Use `@.claude` in PR comments to trigger Claude Code actions
- Automate documentation updates and CLAUDE.md additions through PRs

## Resources

- [Claude Code DeepLearning.ai Course](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant/)
- [Getting Better at Using Coding Agents](https://sankalp.bearblog.dev/my-experience-with-claude-code-20-and-how-to-get-better-at-using-coding-agents/)
- [Boris Cherny's Original Thread](https://x.com/bcherny/status/2007179832300581177)
