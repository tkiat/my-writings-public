# Remove Files from a Repository with Git filter-branch

You might have commited and pushed sensitive data such as password or unwanted big files to the remote repository. It is futile to delete the file and commit again because the deleted item is still in the Git history.

## Brainless Solution

The easiest is of course re-creating a new repository and copy only what you want to this new repository. This is not possible if you or some other devlopers depend on your repositoy history.

## git filter-branch

It rewrite branch history. Let's say I have GO project and I accidentally commit the repositoy with unwanted `server` file. Running

```bash
git filter-branch --tree-filter 'rm -f server' HEAD
```

will check out each commit, run the filter (in this case `rm -f server`), add changes to the index, and commit those changes. It will go from the beginning of history to the HEAD. However, if you know the commit that introduces the unwanted files (say abc123), you can rewrite Git history from that commit to HEAD, i.e.

```bash
git filter-branch --tree-filter 'rm -f server' abc123..HEAD
```

You need to force push afterwards because your local repository is much diverged form the remote. The change is also highly disruptive if some other devlopers depend on your branch. The change applies to a branch only, you need to repeat the process for every branch containing unwanted files.

## Caveat

git filter-branch has some complicated issues that can corrupt your repository (see `man git filter-branch`). Instead, Git project recommends https://github.com/newren/git-filter-repo/.
