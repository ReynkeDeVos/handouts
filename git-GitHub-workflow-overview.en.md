# Git Command Overview (Basics & PR Flow)

## Start a project (initial setup)

```bash
git init
git add .
git commit -m "first commit"
```

---

## Create a remote on GitHub & connect it

1. Create a repo on GitHub (Owner + Name)

1. Link your local repository to the GitHub repository:

```bash
git remote add origin git@github.com:yourGHhandle/example.git
git branch -M main
git push -u origin main
```

> **Note:** `-u` sets the upstream tracking. After that, `git push` is enough on the `main` branch.

3. **Protect the `main` branch** so merges only happen via Pull Requests

   On GitHub: Repo Settings → Branches → create a “Branch protection rule” for `main`/default (e.g. “Require a pull request before merging”).

---

## Develop a feature (branch workflow)

```bash
git switch -c <branchname>
git add . && git commit -m "this is my change"  # save changes
git push -u origin <branchname>  # create a Pull Request OR update an existing PR (GitHub/git will recognize the same branch)
```

- Create the Pull Request on GitHub if there isn’t one yet

---

## Resolve merge conflicts (locally)

```bash
git switch main
git pull  # sync local main with the GitHub repo (shorthand for: git fetch && git merge)
git switch <branchname>
git merge main  # merge updated main into your feature branch & trigger conflicts (if any)
```

ALTERNATIVE:

```bash
# On your <branchname>
git pull origin main  # shorthand for: git fetch origin && git merge origin/main
```

- Resolve conflicts in VS Code’s Merge Editor ([watch tutorial video](https://www.youtube.com/watch?v=HosPml1qkrg))

```bash
git add . && git commit -m "resolve merge conflicts"
git push origin <branchname>  # update the PR with your conflict resolution
```

> This makes it safe to merge the branch online into `main` or `staging`.

---

## Oops! I worked directly on `main`

```bash
git switch -c <branchname>
git push -u origin <branchname>
git switch main
git fetch origin  # downloads the newest metadata/commits from GitHub
git reset --hard origin/main  # resets your local main branch to exactly match origin/main
git switch <branchname>  # keep working on the correct branch
```

---

## Useful shortcuts & checks

```bash
git status  # current state
git log --oneline --graph  # compact history with branch graph
```

---

## 💡 Tip for advanced users: Solve conflicts using `git rebase` (instead of `merge`)

If you want to bring your branch up to date with `main`, you have two options:

- `git merge`
- `git rebase`

## 🧩 Example situation

You have a feature branch:

```text
main:    A --- B --- C
          \
feature:    D --- E
```

In the meantime, new commits (`B`, `C`) were added to `main`.
Now you want to update your branch.

---

## 💥 Option 1: Merge

```bash
# On your <branchname>
git pull origin main  # shorthand for: git fetch origin && git merge origin/main
# resolve conflicts if needed
git push
```

This results in:

```text
main:    A --- B --- C ------ PRM (GitHub PR Merge)
           \               \  /
feature:    D --- E ------- M
                             ↑
                       Merge commit (main→feature)
```

✅ **Works great**
❌ **But:** The history becomes branched and can get a bit harder to read.

---

## 🌿 Option 2: Rebase

```bash
# On your <branchname>
git pull --rebase origin main  # shorthand for: git fetch origin && git rebase origin/main
```

Git “takes” your commits (`D`, `E`), puts them aside, moves to the newest `main`, and then “replays” your changes on top:

```text
main:    A --- B --- C ------- PRM (GitHub PR Merge)
                      \        /
feature:               D' --- E'
```

✅ **Linear history**
❌ **Commit IDs change** → only do this if you’re the only person working on that branch!

---

### ⚙️ If conflicts happen

During a rebase, Git will stop at each conflict:

```bash
# Resolve conflicts in VS Code
git add .
git rebase --continue
```

If you want to abort:

```bash
git rebase --abort
```

---

### 🚀 Push afterwards

Because rebase rewrites history, you need `--force-with-lease`:

```bash
git push --force-with-lease  # after a rebase (history was rewritten)
git push                    # if you only added new commits (no rebase)
```

💡 **Safer than `--force`**, because Git checks whether someone else pushed in the meantime.

---

## 🧠 When to use `rebase` vs `merge`?

| Situation                              | Recommendation |
| -------------------------------------- | -------------- |
| You work alone on your branch          | `rebase`       |
| You work in a team on the same branch  | `merge`        |
| You want a clean, linear PR history    | `rebase`       |
| You want conflicts to be very explicit | `merge`        |

---

## 📘 In short

- **`merge`** = simple, safe, but adds extra merge commits
- **`rebase`** = elegant & linear, but rewrites history
