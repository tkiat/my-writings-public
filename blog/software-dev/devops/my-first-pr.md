# Contribute to Open Source: My First Pull Request

The beauty of open-source software is that everyone can contribute to the codebase. One day, I was about to multi-boot my Linux distros with LVM and LUKS1 so I searched for the relavant commands using the `tldr` package. `tldr` is a concise version of a man page of typically less than 8 bullet-point entries per command. I couldn't find the entry `lvremove` so I took a plunge to add the entry myself.

I finally got it merged and the feeling after that was wonderful! This particular contribution is very simple as it is a stripped down version of a man page yet it is impactful because `tldr` is quite a popular package (based on GitHub stars). You don't have to be an expert to contribute something to open source!

## Steps

- Fork the `tldr` repository from GitHub.
- Clone to my local machine.
- Read `Contributing.md`. It guides contributors on how to contribute. Many projects have this file.
- Make a new branch with the same name as the command I would like to add. In this case, `lvremove`.
- Switch the `lvremove` branch and add a new page. LVM is available for Linux only so I put it in the `linux` folder.
- Push to my remote repository
- On GitHub, click `Pull Requests` tab.
- Click `New Pull Request` and then request to merge it. It had to pass the Continuous Integration check (if any) first.
- Wait until two reviewers (the number depends on the project) came and gave feedbacks. In my case, it was a minor change.
- Revise and commit again to the same branch.
- Wait until they merge to their repo. Done!

Note: If the change is small enough, you might prefer open PR directly on GitHub without cloning the repository.
