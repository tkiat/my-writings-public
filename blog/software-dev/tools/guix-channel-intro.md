# Guix: Build Your Own Channel

A Guix channel is a cool feature from Guix System in which anyone can define his/her packages in a Guile format and aggregate them into one channel and host it on a Git repository. An optional signature can be added so that a user of your channel can verify that they actually download from the actual channel.

I create my channel because I have some packages that I forked from suckless. I can then define this custom channel in ~/.config/guix/channels.scm and whenever I do `guix pull`, the operating system will synchronize all packages from the Git repository where the channel is hosted to the local machine. You can add as many channels as you like. The official documentation covers sufficient details of how to create a Guix channel. I would like to cover a bit more here.

## Channel with Signature

A signature grants a downloader the ability to verify the source. It is optional but once you do, you need to sign a Git commit every time after a certain commit you choose. Here is how you set up Git signing in a repository (in your case the last line might be `gpg.program gpg2` if your package manager names it as such).

```bash
git config commit.gpgsign true
git config user.signingkey <your PGP fingerprint>
git config --global gpg.program gpg
```

After that always sign Git commit by typing `git commit -S -m "my commit details"`

Below is my channels.scm with the authentication after the commit with SHA1 value of 1f5396b846107430c9443ab26900d88263c3f4d3

```scheme
(cons*
  (channel
  (name 'tkiat)
  (url "https://gitlab.com/tkiat/guix-channel.git")
  (branch "master")
  (introduction
    (make-channel-introduction
    "1f5396b846107430c9443ab26900d88263c3f4d3"
    (openpgp-fingerprint
      "A180 8C8D E727 2D87 15CD  AB96 39AA 7B97 9BCC 55C5"))))
  %default-channels)
```

Sometimes you might forget to sign a Git commit and this will break your Guix channel signature. Don't worry, just perform a signed commit again but this time update a commit hash value in the make-channel-introduction field.

## Continuous Integration with Git Hooks

It might be tiresome to change both Guix hash and commit hash manually on the Guix channel whenever you update a package in the channel. That's where automation comes in. Without a local copy of the repository, we need to depend on other Git platform CI. I like to focus on local automation only, which means I need a local copy of a package that I want to update and a local copy of my Guix channel.

A simple Git hook is sufficient for local automation. Removing `.sample` part from a filename in .git/hooks will enable a particular Git hook. We want to update the Guix channel after pushing to the current package remote; however, Git hook has no post-push hook so we will use a pre-push hook instead.

<figure>
  <img src="https://raw.githubusercontent.com/tkiat/my-writings-public/main/blog-data/image/guix-channel-git_hook.png" alt="git hook flow" style="background-color: black">
  <figcaption>The Flow</figcaption>
</figure>

```bash
#!/usr/bin/env dash
guix_repo=/home/$USER/Git/guix-channel
file="$guix_repo"/tkiat/packages/$(basename "$(pwd)").scm

exec < /dev/tty
[ -e "$file" ] && read -p "Do you want to update this commit to your Guix channel (auto-commit and push it)? (y or anything else): " -r ans

if [ "$ans" = "y" ]; then
  git_hash=$(git -C . log -n 1 --format=format:"%H")
  guix_hash=$(guix hash --hash=sha256 --format=nix-base32 -rx .)

  echo "New Git hash: $git_hash. Substituting ..."
  sed -i "s/(commit \"[0-9a-f]\{40\}\")/(commit \"$git_hash\")/g" $file
  echo "New Guix hash: $guix_hash. Substituting ..."
  sed -i "s/(base32 \".\+\")/(base32 \"$guix_hash\")/g" $file

  branch_name=$(git -C "$guix_repo" rev-parse --abbrev-ref HEAD)
  echo "Now at your guix channel ..."
  echo "git add"
  git -C "$guix_repo" add -A
  echo "git signed commit"
  git -C "$guix_repo" commit -S -m "UPDATE: $(basename "$(pwd)").scm"
  echo "git push"
  git -C "$guix_repo" push origin $branch_name
fi
```

<center>pre-push script</center>

Every Git hook script inside .git directory is not pushed to the remote. You can create another folder, say .githooks, and enter `git config core.hooksPath .githooks` to tell Git this is your new Git hook folder, then move the script to this folder.
