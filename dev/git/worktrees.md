Run multiple Claude Code instances simultaneously on the same machine, each working on a different feature branch with fully isolated environments.

  ## The Problem

  When working with AI coding assistants like Claude Code, you often want to:
  - Work on multiple features in parallel
  - Keep each feature's changes isolated
  - Avoid context switching and branch juggling

  Git worktrees solve this by letting you check out multiple branches simultaneously in separate directories.

  ## How Git Worktrees Work

  Instead of one directory switching between branches:

  /Code/myapp/              # main branch (primary worktree)
  /Code/myapp--feature-a/   # feature-a branch (separate worktree)
  /Code/myapp--feature-b/   # feature-b branch (separate worktree)

  Each directory is a full working copy sharing the same `.git` data. Changes in one don't affect the others.

  ## Rails-Specific Challenge: Databases

  If two worktrees share the same database, they'll conflict when schemas diverge:

  1. You add a migration in `myapp--notifications`
  2. Run `rails db:migrate` — adds `notifications` table
  3. Switch to `myapp` (main) — Rails thinks migration ran, but code doesn't know about the table
  4. Tests fail, queries break

  **Solution:** Dynamic database names based on directory.

  ### Update `config/database.yml`

  ```yaml
  development:
    <<: *default
    database: myapp_development_<%= File.basename(Rails.root) %>

  test:
    <<: *default
    database: myapp_test_<%= File.basename(Rails.root) %>

  This produces:
  - /Code/myapp/ → myapp_development_myapp
  - /Code/myapp--notifications/ → myapp_development_myapp--notifications

  Each worktree gets its own isolated database.

  Shell Functions (DHH-style)

  Add these to your ~/.zshrc (or ~/.bashrc):

  # Git worktree helpers for Rails
  # Based on https://gist.github.com/dhh/18575558fc5ee10f15b6cd3e108ed844

  ga() {
    local branch=$1
    [[ -z "$branch" ]] && echo "Usage: ga <branch-name>" && return 1

    # Note: use 'worktree_path' not 'path' — $path is reserved in zsh
    local worktree_path="../$(basename "$PWD")--$branch"

    git worktree add -b "$branch" "$worktree_path"
    cd "$worktree_path"

    # Rails setup
    bundle install
    rails db:prepare

    echo "✓ Worktree ready: $branch"
  }

  gd() {
    local root=$(echo "$PWD" | sed 's/--.*//')
    local branch=$(echo "$PWD" | sed 's/.*--//')

    [[ "$PWD" != *"--"* ]] && echo "Not in a worktree" && return 1

    echo "Remove worktree and delete branch '$branch'? (y/n)"
    read -r confirm
    [[ "$confirm" != "y" ]] && return

    cd "$root"
    git worktree remove "$root--$branch"
    git branch -D "$branch"

    echo "✓ Removed: $branch"
  }

  After adding, run source ~/.zshrc to load the functions.

  zsh Note

  In zsh, $path is a special array variable tied to $PATH. Using local path=... breaks command lookup with errors like zsh: command not found: git. Always use worktree_path or another name.

  Usage

  Create a new worktree

  cd /Code/myapp        # start in your main project
  ga notifications      # creates branch + worktree + database

  This:
  1. Creates branch notifications
  2. Creates directory /Code/myapp--notifications/
  3. Runs bundle install
  4. Creates and prepares the database
  5. Moves you into the new directory

  Open Claude Code

  # Terminal 1
  cd /Code/myapp--notifications
  claude

  # Terminal 2  
  cd /Code/myapp--reports
  claude

  Each instance works independently with its own branch and database.

  Run development servers

  Start servers on different ports:

  # In myapp--notifications
  rails s -p 3001

  # In myapp--reports  
  rails s -p 3002

  Remove a worktree when done

  cd /Code/myapp--notifications
  gd    # prompts for confirmation, removes worktree + branch

  Manual Git Worktree Commands

  If you prefer not to use the shell functions:

  # Create worktree with new branch
  git worktree add ../myapp--feature -b feature

  # Create worktree for existing branch
  git worktree add ../myapp--feature feature

  # List all worktrees
  git worktree list

  # Remove a worktree
  git worktree remove ../myapp--feature
  git branch -D feature    # if you also want to delete the branch

  # Clean up stale worktree references
  git worktree prune

  Workflow Tips

  1. Keep main worktree on main — useful for quick reference and PRs
  2. Name branches descriptively — they become directory names
  3. Each worktree can have its own .env — useful for different API keys/configs
  4. Commit and push regularly — worktrees share git history, so commits are visible everywhere
  5. Merge via the main worktree — switch to main, pull the feature branch, push

  Summary

  | Command           | What it does                                    |
  |-------------------|-------------------------------------------------|
  | ga feature-name   | Create worktree + branch + database, cd into it |
  | gd                | Remove current worktree and its branch          |
  | git worktree list | Show all active worktrees                       |

  This setup gives you fully isolated Rails environments for each feature, perfect for running multiple Claude Code sessions in parallel.

  There you go — a complete guide ready to share. It covers the concept, the Rails database solution, the shell functions with the zsh fix, and practical usage tips.