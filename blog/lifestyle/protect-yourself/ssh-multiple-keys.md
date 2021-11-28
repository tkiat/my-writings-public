# You Should Use Multiple SSH Keys

Having many SSH keys set up for multiple servers like one for GitHub, another for GitLab, that one for AWS, this one for your own DigitalOcean server can be tiresome. I once thought of having only one key because anyway, the private key remained private on my encrypted machine (without /boot). However, I discovered that multiple keys are advisable from my research on the internet.

## When You Leave Your Private Key(s) on Multiple Computers

You should separate your personal SSH keys from your work SSH keys if they are stored in different locations. This will leave less room for compromise.

## Be Aware of SSH Agent Forwarding

SSH agent forwarding is the process in which you grant server A that you are connecting to the ability to connect to server B directly with the same key. The problem is that the SSH client on server A will have access to your local ssh-agent to perform authentication on its behalf. This means, if you have only one key, the rogue user on server A can impersonate you and connect to many other websites.

In reality, it is hard to have control of who is the admin of server A so it is advisable to have multiple SSH keys, one for each server.
