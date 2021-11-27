# My First Ever Accepted Pull Request

My first pull request (PR) is adding a new entry to tldr which is simply a concise version of a man page. One day I was about to multi-boot my Linux distros with LVM and LUKS1 so I searched for LVM-related commands using a tldr package. I couldn't find the entry `lvremove` so I took a plunge to add the entry myself. The feeling after my PR get merged is wonderful!

## Steps

- Fork the tldr repository form GitHub.
- Clone to my local machine.
- Read `Contributing.md`. It guides contributors of how to contribute.
- Make a new branch with the same name as the command I would like to add. In this case, lvremove.
- Switch the lvremove branch and add a new page. LVM is available for Linux only so I put it in the `linux` folder.
- Push to my remote repository
- On GitHub, click `Pull Requests` tab.
- Click `New Pull Request` and then request to merge it. It had to pass the Continuous Integration check (if any) first.
- Wait until two reviewers (the number depends on the project) came and gave feedbacks. In my case, it was minor changes.
- Revise and commit again to the same branch.
- Wait until they merge to their repo. Done!
