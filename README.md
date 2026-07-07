# recap

Get back up to speed on what you were working on.

`recap` is a CLI tool that scans your git repos for recent branch activity — commits, uncommitted changes, stashes, TODOs — and can pipe all that context to [Claude Code](https://claude.com/claude-code) for an AI-powered narrative briefing. It even pulls in your recent Claude Code conversation history per branch, so the briefing knows what you were planning, not just what you committed.

## Features

- 📋 Summarizes recent activity per branch: commits ahead/behind base, files changed, TODOs/FIXMEs introduced
- ⚠️ Flags uncommitted/unstaged changes, untracked files, and stashes
- 🗂 Scans a single repo or every git repo under a directory
- 🤖 `--ai` mode pipes full context (diffs, status, stashes, and matching Claude Code conversations) to Claude for a narrative "here's where you left off" briefing
- 💬 `-i` opens an interactive Claude Code session pre-loaded with that context

## Requirements

- `git`
- `jq` (for reading Claude Code conversation history)
- [Claude Code](https://claude.com/claude-code) CLI (`claude`) — only needed for `--ai` mode

## Install

```bash
git clone https://github.com/njpastrone/recap.git
cd recap
chmod +x recap
# put it on your PATH, e.g.:
ln -s "$PWD/recap" /usr/local/bin/recap
```

Or just copy the script anywhere on your `PATH`:

```bash
curl -fsSL https://raw.githubusercontent.com/njpastrone/recap/main/recap -o /usr/local/bin/recap
chmod +x /usr/local/bin/recap
```

## Usage

```bash
recap                  # scan current repo, last 3 days
recap -d 7             # look back 7 days
recap -r ~/projects    # scan all git repos under a directory (recursive)
recap -b develop       # compare branches against 'develop' instead of auto-detected main/master
recap -a               # show all branches, not just ones you authored
recap -c 10            # show up to 10 commits per branch
recap --ai             # pipe everything to Claude for a narrative briefing
recap --ai -i          # open an interactive Claude Code session with the context loaded
recap -h               # help
```

### Examples

**Monday-morning check on one repo:**

```bash
cd ~/work/my-app
recap -d 4
```

Shows each branch you touched in the last 4 days, with recent commits, ahead/behind counts, TODOs introduced, and any uncommitted changes or stashes.

**Survey everything you touched last week:**

```bash
recap -r ~/Desktop/Development\ Projects -d 7
```

**AI briefing on where you left off:**

```bash
cd ~/work/my-app
recap --ai
```

Claude receives the branch diffs, working-directory status, stashes, and excerpts of your recent Claude Code sessions per branch, then answers: what's in progress, what needs attention, what the untracked files probably are, whether stashes still matter, and what to work on first.

**Continue working with full context:**

```bash
recap --ai -i
```

Same as above, but drops you into an interactive Claude Code session so you can go straight from briefing to work.

## How the Claude Code integration works

Claude Code stores session transcripts under `~/.claude/projects/`. `recap` maps your repo path to its transcript folder, finds sessions tagged with each branch (`gitBranch`), and includes the tail of the most recent conversation in the AI context — including "orphaned" conversations for branches that no longer exist locally (merged, rebased, or abandoned work).

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `-d DAYS` | Look back N days | 3 |
| `-r DIR` | Scan all git repos under DIR | current repo |
| `-b BRANCH` | Base branch to compare against | auto-detect main/master/develop |
| `-a` | Include branches authored by others | off |
| `-c N` | Max commits shown per branch | 5 |
| `--ai` | Pipe context to Claude for a briefing | off |
| `-i` | With `--ai`: interactive Claude Code session | off |

## License

MIT
