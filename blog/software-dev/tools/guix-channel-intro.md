# Guix: Build Your Own Channel

A Guix channel is a cool feature from Guix System in which anyone can define his/her packages in a Guile format and aggregate them into one channel and host it on a Git repository. An optional signature can be added so that a user of your channel can verify that they actually download from the actual channel.

I create my channel because I have some packages that I forked from suckless. I can then define this custom channel in `~/.config/guix/channels.scm`. Whenever I execute `guix pull`, the operating system will synchronize all packages from the Git repository where the channel is hosted to the local machine. You can add as many channels as you like. The official documentation covers sufficient details of how to create a Guix channel. I would like to cover a bit more here.

## Channel With a Signature

A signature grants a downloader the ability to verify the source. It is optional but if you need it, you have to sign a Git commit every time after a certain commit you choose. Here is how you set up Git signing in a repository (the last line might be `gpg.program gpg2` if your package manager names it as such).

```bash
$ git config commit.gpgsign true
$ git config user.signingkey <your PGP fingerprint>
$ git config --global gpg.program gpg
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

## Continuous Integration With Git Hooks

It might be tiresome to change both Guix hash and commit hash manually on the Guix channel whenever you update a package in the channel. That's where the automation comes in. Without a local copy of the repository, we need to depend on other Git platform CI. I like to focus on local automation only, which means I need a local copy of a package that I want to update and a local copy of my Guix channel.

A simple Git hook is sufficient for local automation. Removing `.sample` part from a file name in .git/hooks will enable a particular Git hook. We want to update the Guix channel after pushing to the current package remote; however, Git has no post-push hook so we will use a pre-push hook instead.

<figure>
  <svg style="width: max(50%, 240px);" viewBox="0 0 225.5 291"><g class="graph graphviz" transform="translate(4 287)"><g class="node graphviz__text"><path fill="none" stroke="#000" d="M153-283H78v36h75v-36z"/><text x="115.5" y="-261.3" font-family="Times,serif" font-size="14" text-anchor="middle">git push</text></g><g class="node graphviz__text"><path fill="none" stroke="#000" d="M115.5-196l-70.55 18 70.55 18 70.55-18-70.55-18z"/><text x="115.5" y="-174.3" font-family="Times,serif" font-size="14" text-anchor="middle">Update?</text></g><g class="edge graphviz__arrow"><path fill="none" stroke="#000" d="M115.5-246.8v40.56"/><path stroke="#000" d="M119-206.18l-3.5 10-3.5-10h7z"/><text x="166.5" y="-217.8" font-family="Times,serif" font-size="14" text-anchor="middle">pre-push hook</text></g><g class="node graphviz__text"><path fill="none" stroke="#000" d="M129-109H0v36h129v-36z"/><text x="64.5" y="-87.3" font-family="Times,serif" font-size="14" text-anchor="middle">Update channel</text></g><g class="edge graphviz__arrow"><path fill="none" stroke="#000" d="M106.62-162.19c-7.26 12.09-17.7 29.49-26.37 43.94"/><path stroke="#000" d="M83.01-116.05l-8.15 6.78 2.15-10.38 6 3.6z"/><text x="106" y="-130.8" font-family="Times,serif" font-size="14" text-anchor="middle">Yes</text></g><g class="node graphviz__text"><path fill="none" stroke="#000" d="M174-36H55c-6 0-12 6-12 12v12c0 6 6 12 12 12h119c6 0 12-6 12-12v-12c0-6-6-12-12-12"/><text x="114.5" y="-14.3" font-family="Times,serif" font-size="14" text-anchor="middle">Pushed to remote</text></g><g class="edge graphviz__arrow"><path fill="none" stroke="#000" d="M122.8-161.51c8.65 20.31 21.12 57.05 14.7 88.51-1.9 9.29-5.45 18.95-9.21 27.49"/><path stroke="#000" d="M131.45-43.99l-7.41 7.57 1.07-10.54 6.34 2.97z"/><text x="150" y="-87.3" font-family="Times,serif" font-size="14" text-anchor="middle">No</text></g><g class="edge graphviz__arrow"><path fill="none" stroke="#000" d="M76.6-72.81c6.02 8.54 13.41 19.04 20.07 28.49"/><path stroke="#000" d="M99.61-46.22l2.9 10.19-8.62-6.16 5.72-4.03z"/></g></g></svg>
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
  sed -i "s/(commit \"[0-9a-f]\{40\}\")/(commit \"$git_hash\")/g" "$file"
  echo "New Guix hash: $guix_hash. Substituting ..."
  sed -i "s/(base32 \".\+\")/(base32 \"$guix_hash\")/g" "$file"

  branch_name=$(git -C "$guix_repo" rev-parse --abbrev-ref HEAD)
  echo "Now at your guix channel ..."
  echo "git add"
  git -C "$guix_repo" add -A
  echo "git signed commit"
  git -C "$guix_repo" commit -S -m "UPDATE: $(basename "$(pwd)").scm"
  echo "git push"
  git -C "$guix_repo" push origin "$branch_name"
fi
```

<center>pre-push script</center>

Every Git hook script inside .git directory is not pushed to the remote. You can create another folder, say `.githooks`. Then, you can execute the command `git config core.hooksPath .githooks` to tell Git about this new hook folder. Finally, you can move the above script to this folder.
