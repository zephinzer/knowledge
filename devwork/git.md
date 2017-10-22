# Git

1. [Code Logistic Models](#code-logistic-models)
2. [Troubleshooting](#troubleshooting)
3. [Atomics (Glossary)](#atomics-glossary)

- - -

## Code Logistic Models

### Feature Branching

1. Create a new branch based off `development` branch following your organisation's branch naming conventions
    - `git checkout development`
    - `git checkout -b feature_123456789`
2. Commence work on new branch, making **only** commits and not pushes
    - `git commit -m "some work done"`
3. When ready to merge with `development`, commit all files and squash them (find out how many commits have been made since the branching and substitute `__NUMBER_OF_COMMITS__` with that number)
    - `git rebase -i HEAD~__NUMBER_OF_COMMITS__`
4. Checkout `development` branch and pull to get the latest code
    - `git checkout development && git pull --rebase origin development`
5. Checkout `feature` branch and do a local rebase from `development` branch
    - `git checkout feature_123456789 && git rebase development`
4. Push to the feature branch
    - `git push origin feature_123456789`
5. Create a merge request from your source control remote

- - -

## Troubleshooting

### Handling merge conflicts

#### Symptom
On pull, you get a message that resembles the following:

- `CONFLICT (content): Merge conflict in some-file`
- `CONFLICT (submodule): Merge conflict in some-submodule/some-file`

#### Diagnosis
Get the repo status by running:

`git status`

See the merge conflicts by running:

`diff --cc some-file` or `diff --cc some-submodule/some-file`

#### Solution

1. List files with merge conflicts
    - `git status`
2. Search for `>>>>` in all files listed from (1) and resolve the conflicts
3. Repeat 1 and 2 until all confclits have been resolved
4. Stage all files where conflicts have been resolved
    - `git add __PATH_TO_FILE__`
5. Resume the merge
    - `git merge --continue`
6. In case of conflict resolution that causes further issues, abort the merge with:
    - `git merge --abort`

### Published branches not getting fetched

#### Symptom
Running `git fetch` does not fetch all branches even if they show up in the remote repository.

#### Diagnosis
`git config --get remote.__REMOTE_NAME__.fetch`

#### Solution
`git config remote.__REMOTE_NAME__.fetch "+refs/heads/*:refs/remotes/__REMOTE_NAME__/*"`

- - -

## Atomics (Glossary)


### Add a new remote
`git remote add __REMOTE_NAME__`

### Checkout an existing branch
`git checkout __BRANCH_NAME__`

### Checkout a new branch
`git checkout -b __BRANCH_NAME__`

### Checkout a new branch tracking a specific remote's branch
`git checkout --track -b __CUSTOM_BRANCH_NAME__ __REMOTE_NAME__/__BRANCH_NAME__`

### Clone a repository into default directory
`git clone __REMOTE_URL__`

### Clone a repository into a named directory
`git clone __REMOTE_URL__ __DIRECTORY_NAME__`

### Configure your email
`git config --global user.email "you@email.com"`

### Configure your name
`git config --global user.name "your name"`

### Create a commit without files
`git commit -m "commit message" --allow-empty"`

### Delete a local branch forcefully
`git branch -D __BRANCH_NAME__`

### Delete a local branch gently
`git branch -d __BRANCH_NAME__`

### Delete a published branch
`git push -d __REMOTE_NAME__ __BRANCH_NAME__`

`git fetch --all --prune`

### Delete all changes since branching
`git fetch __REMOTE__NAME__ && git reset --hard FETCH_HEAD && git clean -df`

### Fetch changes from remote repository
`git fetch __REMOTE_NAME__`

### Get all branches
`git branch -av`

### Get all remotes
`git remote -v`

### Ignore a file
`touch .gitignore && echo __FILENAME__ >> .gitignore`

### Ignore changes in a file for current commit
`git update-index --skip-worktree __FILENAME__`

### Pull via Merge (fetch changes from remote and merge via merge)
`git pull __REMOTE_NAME__ __BRANCH_NAME__`

### Pull via Rebase (fetch changes from remote and merge via rebase)

`git pull --rebase __REMOTE_NAME__ __BRANCH_NAME__`

### Push local commits to remote
`git push __REMOTE_NAME__ __BRANCH_NAME__`

### Push local commits to remote forcefully
`git push -f __REMOTE_NAME__ __BRANCH_NAME__`

### Merge changes from local branch
`git merge __BRANCH_NAME__`

### Rebase changes from local branch
`git rebase __BRANCH_NAME__`

### Untrack a file
`git rm --cached __PATH_TO_FILE__`