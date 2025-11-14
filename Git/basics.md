# Git & GitHub Complete Commands Guide

## 1. Git Fundamentals

### What is Git?
- **Distributed Version Control System** (DVCS)
- Created by **Linus Torvalds** (2005)
- Tracks changes in source code
- Enables collaboration among developers
- Local repository on every developer's machine

### What is GitHub?
- **Web-based hosting service** for Git repositories
- Provides collaboration features
- Pull requests, issues, projects
- CI/CD with GitHub Actions
- Alternatives: GitLab, Bitbucket

### Git vs GitHub

| Git | GitHub |
|-----|--------|
| Version control software | Hosting service |
| Installed locally | Cloud-based |
| Command-line tool | Web interface + API |
| Manages repositories | Hosts repositories |

---

## 2. Git Installation & Configuration

### Installation

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install git

# RHEL/CentOS
sudo yum install git

# macOS (Homebrew)
brew install git

# Windows
# Download from git-scm.com

# Verify installation
git --version
```

### Initial Configuration

```bash
# Set username
git config --global user.name "Your Name"

# Set email
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "vim"
git config --global core.editor "nano"
git config --global core.editor "code --wait"  # VS Code

# Set default branch name
git config --global init.defaultBranch main

# View configuration
git config --list
git config user.name
git config user.email

# Configuration levels
git config --global    # User-level (~/.gitconfig)
git config --system    # System-level (/etc/gitconfig)
git config --local     # Repository-level (.git/config)

# Edit config file directly
git config --global --edit
```

### Useful Global Settings

```bash
# Enable color output
git config --global color.ui auto

# Set up aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg 'log --oneline --graph --all'

# Configure line endings
git config --global core.autocrlf input     # Linux/Mac
git config --global core.autocrlf true      # Windows

# Ignore file permissions
git config --global core.fileMode false
```

---

## 3. Basic Git Commands

### Creating Repositories

```bash
# Initialize new repository
git init
git init project-name

# Clone existing repository
git clone <repository-url>
git clone <url> <directory-name>
git clone <url> --branch <branch-name>

# Clone with specific depth (shallow clone)
git clone --depth 1 <url>

# Examples
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git
```

### Basic Workflow

```bash
# Check repository status
git status
git status -s              # Short format
git status --branch        # Show branch info

# Add files to staging area
git add filename
git add file1 file2 file3
git add .                  # All files in current directory
git add -A                 # All files in repository
git add *.txt              # All .txt files
git add -p                 # Interactive staging (patch mode)

# Remove from staging
git reset filename
git reset                  # Unstage all

# Commit changes
git commit -m "Commit message"
git commit -m "Title" -m "Description"
git commit -am "Message"   # Add and commit (tracked files only)
git commit --amend         # Modify last commit
git commit --amend -m "New message"

# View commit history
git log
git log --oneline          # Compact view
git log --graph            # Show branch graph
git log --all --graph --oneline
git log -n 5               # Last 5 commits
git log --since="2 weeks ago"
git log --author="John"
git log --grep="bug"       # Search commit messages
git log -p                 # Show diff
git log --stat             # Show statistics

# Show specific commit
git show <commit-hash>
git show HEAD              # Show last commit
git show HEAD~1            # Show previous commit
```

###Working with Changes

```bash
# View differences
git diff                   # Unstaged changes
git diff --staged          # Staged changes
git diff HEAD              # All changes since last commit
git diff branch1 branch2   # Compare branches
git diff commit1 commit2   # Compare commits

# Discard changes
git checkout -- filename   # Discard changes in file
git checkout .             # Discard all changes
git restore filename       # Newer syntax (Git 2.23+)
git restore --staged file  # Unstage file

# Remove files
git rm filename            # Remove and stage
git rm --cached filename   # Remove from Git, keep in filesystem
git rm -r directory/       # Remove directory

# Move/Rename files
git mv oldname newname
```

---

## 4. Branching & Merging

### Branch Operations

```bash
# List branches
git branch                 # Local branches
git branch -r              # Remote branches
git branch -a              # All branches
git branch -v              # With last commit

# Create branch
git branch <branch-name>
git branch feature/login

# Switch branches
git checkout <branch-name>
git checkout -b <new-branch>  # Create and switch
git switch <branch-name>      # Newer syntax (Git 2.23+)
git switch -c <new-branch>    # Create and switch

# Rename branch
git branch -m <old-name> <new-name>
git branch -m <new-name>      # Rename current branch

# Delete branch
git branch -d <branch-name>   # Safe delete (merged only)
git branch -D <branch-name>   # Force delete

# Delete remote branch
git push origin --delete <branch-name>
```

### Merging

```bash
# Merge branch into current branch
git merge <branch-name>
git merge feature/login

# Merge strategies
git merge --no-ff <branch>    # Always create merge commit
git merge --squash <branch>   # Combine all commits into one
git merge --ff-only <branch>  # Fast-forward only

# Abort merge (if conflicts)
git merge --abort

# Example workflow
git checkout main
git merge feature/login
```

### Rebasing

```bash
# Rebase current branch onto another
git rebase <branch>
git rebase main

# Interactive rebase
git rebase -i HEAD~3          # Last 3 commits
git rebase -i <commit-hash>

# During rebase
git rebase --continue         # After resolving conflicts
git rebase --abort            # Cancel rebase
git rebase --skip             # Skip current commit

# Rebase options in interactive mode
# pick   - use commit
# reword - use commit, but edit message
# edit   - use commit, but stop for amending
# squash - combine with previous commit
# fixup  - like squash, but discard log message
# drop   - remove commit
```

### Resolving Merge Conflicts

```bash
# When conflict occurs
# 1. Git marks conflicts in files
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> branch-name

# 2. Edit files to resolve
# 3. Stage resolved files
git add <resolved-files>

# 4. Complete merge/rebase
git commit                    # For merge
git rebase --continue         # For rebase

# View conflicts
git diff --name-only --diff-filter=U
git status

# Use merge tools
git mergetool
```

---

## 5. Remote Repositories

### Working with Remotes

```bash
# View remotes
git remote
git remote -v                 # Show URLs

# Add remote
git remote add <name> <url>
git remote add origin https://github.com/user/repo.git

# Remove remote
git remote remove <name>

# Rename remote
git remote rename <old> <new>

# Change remote URL
git remote set-url origin <new-url>

# Show remote info
git remote show origin
```

### Fetching & Pulling

```bash
# Fetch changes (doesn't merge)
git fetch
git fetch origin
git fetch origin <branch>
git fetch --all               # All remotes

# Pull changes (fetch + merge)
git pull
git pull origin main
git pull --rebase             # Rebase instead of merge
git pull --ff-only            # Fast-forward only

# Example workflow
git fetch origin
git merge origin/main
# OR
git pull origin main
```

### Pushing

```bash
# Push to remote
git push
git push origin main
git push origin <branch>
git push -u origin main       # Set upstream
git push --all                # All branches
git push --tags               # All tags

# Force push (dangerous!)
git push --force
git push --force-with-lease   # Safer force push

# Delete remote branch
git push origin --delete <branch>
```

---

## 6. Git Tags

### Creating Tags

```bash
# Lightweight tag
git tag v1.0.0
git tag v1.0.0 <commit-hash>

# Annotated tag (recommended)
git tag -a v1.0.0 -m "Version 1.0.0 release"
git tag -a v1.0.0 <commit-hash> -m "Message"

# List tags
git tag
git tag -l "v1.*"             # Filter tags

# Show tag info
git show v1.0.0

# Push tags
git push origin v1.0.0        # Push single tag
git push origin --tags        # Push all tags

# Delete tags
git tag -d v1.0.0             # Delete local
git push origin --delete v1.0.0  # Delete remote

# Checkout tag
git checkout v1.0.0
git checkout -b branch-name v1.0.0  # Create branch from tag
```

---

## 7. Advanced Git Commands

### Stashing

```bash
# Save changes temporarily
git stash
git stash save "Work in progress"
git stash -u                  # Include untracked files
git stash -a                  # Include all files

# List stashes
git stash list

# Apply stash
git stash apply               # Apply latest, keep in stash
git stash apply stash@{2}     # Apply specific stash
git stash pop                 # Apply and remove from stash

# Show stash contents
git stash show
git stash show -p stash@{1}   # Show diff

# Drop stash
git stash drop stash@{1}
git stash clear               # Remove all stashes

# Create branch from stash
git stash branch <branch-name>
```

### Cherry-picking

```bash
# Apply specific commit to current branch
git cherry-pick <commit-hash>
git cherry-pick abc123 def456  # Multiple commits

# Cherry-pick range
git cherry-pick start-commit..end-commit

# Options
git cherry-pick --no-commit <hash>  # Don't auto-commit
git cherry-pick --continue          # After resolving conflicts
git cherry-pick --abort             # Cancel operation
```

### Reverting & Resetting

```bash
# Revert commit (creates new commit)
git revert <commit-hash>
git revert HEAD                # Revert last commit
git revert HEAD~3              # Revert 4th last commit

# Reset (dangerous!)
git reset --soft <commit>      # Keep changes staged
git reset --mixed <commit>     # Keep changes unstaged (default)
git reset --hard <commit>      # Discard all changes

# Common reset scenarios
git reset HEAD~1               # Undo last commit, keep changes
git reset --hard HEAD~1        # Undo last commit, discard changes
git reset --hard origin/main   # Match remote branch

# Reset single file
git checkout <commit> -- filename
```

### Cleaning

```bash
# Remove untracked files
git clean -n                   # Dry run (show what would be deleted)
git clean -f                   # Remove files
git clean -fd                  # Remove files and directories
git clean -fx                  # Remove files including ignored
git clean -fxd                 # Remove everything
```

### Reflog

```bash
# View reference logs (history of HEAD)
git reflog
git reflog show                # Show all refs
git reflog show <branch>

# Recover lost commits
git reflog
git checkout <commit-hash>
git cherry-pick <commit-hash>
```

### Bisect (Binary Search for Bugs)

```bash
# Start bisect
git bisect start
git bisect bad                 # Current commit is bad
git bisect good <commit>       # Known good commit

# Git checks out middle commit
# Test and mark as good or bad
git bisect good
# OR
git bisect bad

# When found
git bisect reset               # Return to original branch

# Automated bisect
git bisect run <test-script>
```

### Submodules

```bash
# Add submodule
git submodule add <repository-url> <path>

# Clone repository with submodules
git clone --recursive <url>

# Initialize submodules in existing repo
git submodule init
git submodule update

# Update submodules
git submodule update --remote

# Remove submodule
git submodule deinit <path>
git rm <path>
```

---

## 8. GitHub Specific Commands

### GitHub CLI (gh)

```bash
# Installation
# Ubuntu/Debian
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh

# macOS
brew install gh

# Authenticate
gh auth login

# Repository operations
gh repo create <name>
gh repo clone <user/repo>
gh repo view
gh repo list

# Pull Request operations
gh pr create
gh pr list
gh pr view <number>
gh pr checkout <number>
gh pr merge <number>
gh pr close <number>

# Issue operations
gh issue create
gh issue list
gh issue view <number>
gh issue close <number>

# Release operations
gh release create v1.0.0
gh release list
gh release view v1.0.0

# Gist operations
gh gist create file.txt
gh gist list
```

### SSH Key Setup

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Test connection
ssh -T git@github.com

# Add to GitHub
# Settings → SSH and GPG keys → New SSH key
# Paste public key
```

### Personal Access Tokens

```bash
# Generate token on GitHub
# Settings → Developer settings → Personal access tokens → Generate new token

# Use token for HTTPS
git clone https://<token>@github.com/user/repo.git

# Or configure credential helper
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'

# Store credentials (less secure)
git config --global credential.helper store
```

---

## 9. Git Workflows

### Feature Branch Workflow

```bash
# 1. Create feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "Add new feature"

# 3. Push to remote
git push -u origin feature/new-feature

# 4. Create pull request on GitHub

# 5. After review, merge on GitHub or locally
git checkout main
git pull origin main
git merge feature/new-feature

# 6. Delete branch
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### Gitflow Workflow

```
main (production)
  ↓
develop (integration)
  ↓
feature branches
  ↓
release branches
  ↓
hotfix branches
```

```bash
# Start new feature
git checkout -b feature/login develop

# Finish feature
git checkout develop
git merge --no-ff feature/login
git branch -d feature/login

# Start release
git checkout -b release/1.0 develop
# Bump version, fix bugs

# Finish release
git checkout main
git merge --no-ff release/1.0
git tag -a v1.0 -m "Version 1.0"
git checkout develop
git merge --no-ff release/1.0
git branch -d release/1.0

# Hotfix
git checkout -b hotfix/1.0.1 main
# Fix bug
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix 1.0.1"
git checkout develop
git merge --no-ff hotfix/1.0.1
git branch -d hotfix/1.0.1
```

### Forking Workflow

```bash
# 1. Fork repository on GitHub

# 2. Clone your fork
git clone https://github.com/your-username/repo.git

# 3. Add upstream remote
git remote add upstream https://github.com/original-owner/repo.git

# 4. Create feature branch
git checkout -b feature/new-feature

# 5. Make changes and commit
git add .
git commit -m "Add feature"

# 6. Push to your fork
git push origin feature/new-feature

# 7. Create pull request on GitHub

# 8. Keep fork updated
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## 10. .gitignore

### Creating .gitignore

```bash
# Create .gitignore file
touch .gitignore

# Common patterns
*.log                 # Ignore all .log files
*.tmp                 # Ignore all .tmp files
node_modules/         # Ignore directory
!important.log        # Don't ignore this file
build/                # Ignore build directory
.env                  # Ignore environment file
*.class               # Java class files
*.pyc                 # Python bytecode
```

### Example .gitignore for Different Projects

**Node.js:**
```
node_modules/
npm-debug.log
.env
dist/
build/
.DS_Store
```

**Python:**
```
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
ENV/
.env
```

**Java:**
```
*.class
*.jar
*.war
target/
.idea/
*.iml
```

**General:**
```
# OS
.DS_Store
Thumbs.db

# IDEs
.vscode/
.idea/
*.swp
*.swo

# Build
dist/
build/
*.log

# Environment
.env
.env.local
```

### Ignore Already Tracked Files

```bash
# Stop tracking file but keep it
git rm --cached filename
git rm --cached -r directory/

# Add to .gitignore
echo "filename" >> .gitignore

# Commit
git commit -m "Stop tracking filename"
```

---

## 11. Git Best Practices

### Commit Messages

**Good commit message structure:**
```
<type>: <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting, missing semicolons, etc.
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**
```bash
git commit -m "feat: add user login functionality"
git commit -m "fix: resolve null pointer exception in payment service"
git commit -m "docs: update API documentation"
```

### Branch Naming Conventions

```
feature/<feature-name>      # feature/user-authentication
bugfix/<bug-name>           # bugfix/login-error
hotfix/<issue-number>       # hotfix/critical-bug-123
release/<version>           # release/v1.2.0
```

### General Best Practices

1. **Commit often**: Small, focused commits
2. **Write clear messages**: Descriptive commit messages
3. **Review before commit**: `git diff --staged`
4. **Pull before push**: Keep local branch updated
5. **Use branches**: Never commit directly to main
6. **Delete merged branches**: Keep repository clean
7. **Use .gitignore**: Don't commit generated files
8. **Don't commit secrets**: Use environment variables
9. **Rebase vs merge**: Know when to use each
10. **Test before commit**: Ensure code works

---

## 12. Troubleshooting Common Issues

### Undo Last Commit

```bash
# Keep changes
git reset --soft HEAD~1

# Discard changes
git reset --hard HEAD~1
```

### Fix Wrong Commit Message

```bash
git commit --amend -m "Correct message"
```

### Add Forgotten Files to Last Commit

```bash
git add forgotten-file
git commit --amend --no-edit
```

### Discard All Local Changes

```bash
git reset --hard HEAD
git clean -fd
```

### Recover Deleted Branch

```bash
git reflog
git checkout -b <branch-name> <commit-hash>
```

### Resolve "Detached HEAD"

```bash
# Create branch at current position
git checkout -b <new-branch-name>

# OR go back to branch
git checkout main
```

### Remove Large File from History

```bash
# Using BFG Repo-Cleaner
bfg --delete-files large-file.zip
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Using git filter-branch (slower)
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch path/to/large-file' \
  --prune-empty --tag-name-filter cat -- --all
```

### Change Author Information

```bash
# Last commit
git commit --amend --author="New Name <email@example.com>"

# Multiple commits
git rebase -i HEAD~3
# Mark commits as 'edit'
# For each commit:
git commit --amend --author="New Name <email@example.com>" --no-edit
git rebase --continue
```

---

## 13. Interview Questions

### Basic Questions

**Q: What is Git?**
- Distributed version control system
- Tracks changes in source code
- Enables collaboration
- Local repository on each machine

**Q: Difference between Git and GitHub?**
- Git: Version control software
- GitHub: Hosting service for Git repositories

**Q: What is a repository?**
- Storage location for project files
- Contains all versions and history
- Can be local or remote

**Q: Explain Git workflow**
1. Modify files (Working Directory)
2. Stage changes (Staging Area)
3. Commit changes (Repository)
4. Push to remote (Remote Repository)

**Q: What is staging area?**
- Intermediate area between working directory and repository
- Preview changes before commit
- Selective commits possible

### Intermediate Questions

**Q: Difference between git pull and git fetch?**
- `git fetch`: Downloads changes, doesn't merge
- `git pull`: Downloads and merges changes (fetch + merge)

**Q: Explain git merge vs git rebase**
- `merge`: Combines branches, preserves history, creates merge commit
- `rebase`: Re-applies commits on top of another branch, linear history

**Q: How to resolve merge conflicts?**
1. Identify conflicted files
2. Edit files to resolve conflicts
3. Stage resolved files (`git add`)
4. Complete merge (`git commit`)

**Q: What is git stash?**
- Temporarily saves changes without committing
- Useful when switching branches
- Can apply later with `git stash pop`

**Q: Explain HEAD in Git**
- Pointer to current branch reference
- Points to latest commit on current branch
- `HEAD~1` means parent of HEAD
- `HEAD~2` means grandparent

### Advanced Questions

**Q: Difference between git reset modes?**
- `--soft`: Moves HEAD, keeps staging and working directory
- `--mixed`: Moves HEAD, resets staging, keeps working directory
- `--hard`: Moves HEAD, resets staging and working directory

**Q: How to find a bug using git bisect?**
```bash
git bisect start
git bisect bad          # Current is bad
git bisect good <hash>  # Known good commit
# Test each checkout
git bisect good/bad     # Mark result
git bisect reset        # When done
```

**Q: What is git cherry-pick?**
- Apply specific commit from one branch to another
- Useful for selectively merging features
- `git cherry-pick <commit-hash>`

**Q: How to rewrite Git history?**
- `git rebase -i`: Interactive rebase
- `git commit --amend`: Modify last commit
- `git filter-branch`: Rewrite multiple commits
- `git reflog`: Recover lost commits

**Q: Explain git hooks**
- Scripts that run automatically on Git events
- Located in `.git/hooks/`
- Types: pre-commit, post-commit, pre-push, etc.
- Use for linting, testing, formatting

---

## Quick Reference Cheat Sheet

```bash
# Configuration
git config --global user.name "Name"
git config --global user.email "email"

# Repository
git init
git clone <url>

# Basic
git status
git add <file>
git commit -m "message"
git push
git pull

# Branching
git branch <name>
git checkout <branch>
git checkout -b <branch>
git merge <branch>
git branch -d <branch>

# Remote
git remote add origin <url>
git remote -v
git push -u origin main
git fetch
git pull

# History
git log
git log --oneline
git diff
git show <commit>

# Undo
git reset --soft HEAD~1
git reset --hard HEAD~1
git revert <commit>

# Stash
git stash
git stash pop
git stash list
```

Good luck with Git & GitHub! 🚀
