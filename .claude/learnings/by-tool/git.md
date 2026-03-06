# Git Learnings

## [2026-01-31] Lesson: Place git worktrees outside the repo as siblings, not inside it
**ID**: a1b2c3d4
**Category**: tool
**Context**: When creating git worktrees for isolated parallel work on the same codebase
**Learning**: Always create worktrees as sibling directories outside the repo (`git worktree add ../repo-name-feature -b branch-name base`), never inside the repo (`git worktree add worktrees/feature`). Placing worktrees inside the repo creates an orphan directory that requires `.gitignore` entries and can confuse other tools. Sibling placement is standard practice and avoids polluting the repo.
**Evidence**: Created `worktrees/no-exceptions/` inside the repo during no-exceptions enforcement work. After cleanup, an empty `worktrees/` directory remained and raised the question of whether it should be in `.gitignore` — a sign it was in the wrong location.
**Confidence**: high
**Validations**: 1
**Projects**: dev-team

## [2026-03-06] Lesson: git filter-repo requires --no-local clone and --force after branch checkouts
**ID**: b3c4d5e6
**Category**: tool
**Context**: Extracting project history from a monorepo using git filter-repo
**Learning**:
- Clone with `git clone --no-local` (not a plain `git clone`) when cloning a local repo for filter-repo extraction, or filter-repo will refuse with "not a fresh clone".
- After creating local tracking branches (e.g. `git checkout prod/feature`), filter-repo also refuses because reflog has multiple entries. Use `--force` to proceed.
- `--path-rename src/:` strips the path prefix so extracted commits look native (files at root, not nested under old monorepo path).
- Branches that never touched the filtered path will be dropped by filter-repo — this is correct behavior.
- If the source branch used a different path (e.g. `blackjack/` pre-restructuring vs `projects/blackjack/` post), include both paths with separate `--path` and `--path-rename` flags.
**Evidence**: Extracted blackjack and codeatlas-cpp from dev-team monorepo. First attempt failed with "not a fresh clone", second failed with "multiple reflog entries", both resolved as described.
**Confidence**: high
**Validations**: 1
**Projects**: dev-team

## [2026-03-06] Lesson: Untracked files survive branch switches and block git submodule add
**ID**: c4d5e6f7
**Category**: tool
**Context**: Converting monorepo project directories to git submodules
**Learning**: When switching branches, Git only removes tracked files that differ between branches. Untracked files (build artifacts, node_modules, dist/) stay on disk and can block `git submodule add` with "already exists and is not a valid git repo". Always `rm -rf` the stale directory before running `git submodule add`.
**Evidence**: After switching from project/code-browser-cpp to master, `projects/codeatlas-cpp/` still existed with dist/, node_modules/, src/ from the feature branch. `git submodule add` failed until we removed the directory.
**Confidence**: high
**Validations**: 1
**Projects**: dev-team
