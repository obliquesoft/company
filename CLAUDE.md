# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About This Repository

This is the Oblique Software company operating system - a knowledge base of development guidelines, best practices, and workflows. It's designed to be shared with AI coding assistants to speed up work.

## Repository Structure

```
dev/                    # Development guidelines and practices
  claude_code.md        # Resources for using Claude Code
  design/               # Design patterns and architecture decisions
    how_to_use_models_in_rails.md
  git/                  # Git workflows
    worktrees.md        # Using git worktrees for parallel development
```

## Key Development Principles

### Rails Architecture

- **No service objects** - Use rich POROs (Plain Old Ruby Objects) instead
- A "model" in Rails doesn't only mean Active Record; it can model any domain concept
- Reference: 37signals "Vanilla Rails is Plenty" approach

### Git Worktrees for Parallel Development

When working on Rails projects with multiple Claude Code instances:
- Use git worktrees to work on multiple feature branches simultaneously
- Configure `database.yml` with dynamic database names based on directory:
  ```yaml
  database: myapp_development_<%= File.basename(Rails.root) %>
  ```
- Shell helpers: `ga <branch>` creates worktree + branch + database, `gd` removes current worktree
