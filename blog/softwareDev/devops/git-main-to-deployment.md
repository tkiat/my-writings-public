# Git: Transfer Build from Main to Deployment Branch

I could have combined my main code and deployment directory into one single branch but that makes it hard to organize the project. Instead, I put all the codes to generate the deployment into the main branch and the deployment itself (I will refer it as dist folder) into the deployment branch.

The thing is, I can't seem to find a direct way to transfer the dist folder directly from the main branch to the deployment branch which already has the folder dist but with different content. In fact, Git prevented me from doing so because of the conflict.

## My Solution

- Build the dist folder using codes in the main branch.
- Move the dist folder to /tmp/dist.
- Now we can switch to the deployment branch. Switch to it. You might have to use `git stash` before if you have uncommitted changes before switching.
- Replace the current dist folder with /tmp/dist.
- Automatically commit and push to the remote repo.
- Go back to the main branch and use `git stash apply` if you performed `git stash` before.

I can automate this using an npm script. Noice!
