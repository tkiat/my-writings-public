# How to Clone a Remote Repository, Modify it, and Keep Them Synced: suckless dwm Example

suckless dwm is a dynamic window manager that a user can fork and add config as he likes. This post guides the basics of how to maintain it.

## Initialize

1. Visit the suckless website and copy their Git repository.

```bash
git clone git://git.suckless.org/dwm
cd dwm
```

2. Create your repository on any Git server you like.
3. Remove suckless remote origin and replace it with yours. Now, your Git directory doesn't bind to the suckless repository anymore.

```bash
git remote remove origin
git remote add origin <your Git URL>
```

4. Commit and push to your remote repository. I will assume your default branch is `main`.
5. I like to create another branch to store my config while leaving default as it is in `main` branch. Let's name that branch my-config

```bash
git checkout my-config
```

6. Push this branch to your remote repository
7. You can optionally store the suckless Git URL that we just deleted. First, add it to the remote.

```bash
git remote add source git://git.suckless.org/dwm
```

8. Git stores this new remote information in .git/config. However, due to security concerns, this change will not propagate to your remote repository whenever you push changes to that. One way is to store that in .gitconfig in the root folder instead. First, find the relevant suckless remote information from .git/config.

```bash
cat .git/config

<whatever above don't care>
[remote "source"]
url = git://git.suckless.org/dwm
fetch = +refs/heads/_:refs/remotes/source/_
```

9. Add this information to .gitconfig. Remember, we want to leave the main branch the same suckless, so this .gitconfig lives only in our my-config branch.

10. Push changes to your remote repository.

## Synchronize with suckless Repository

Later on, suckless developers might update their repository by multiple commits. You probably want to apply those changes to your repository as well. This can be done by using Git rebase (do research if you are not familiar with the term).

1. Run `git remote -v` to find if the suckless Git URL is still in your repository remote. If not, switch to the `my-config` branch and transfer data that you previously stored in .gitconfig to the config in your current Git directory.

```bash
git config --local include.path ../.gitconfig
```

2. Now `git remote -v` should display suckless Git URL. suckless default branch is master (verify by git clone [suckless Git URL] somewhere, cd to it and run git branch command to check their default branch). Now switch to our main branch and pull changes from the suckless remote repository and push changes to our remote repository.

```bash
git checkout main
git pull source master
git push origin main
```

3. What is left to do is rebasing our my-config branch to align with our main branch.

```bash
git checkout my-config
git rebase master
```

4. Force push by appending + in front of our branch name.

```bash
git push origin +my-config
```
