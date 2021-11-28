# Password Manager: Why I Switch from Bitwarden to Pass

## Bitwarden: Introduction

Bitwarden is considered a very awesome open-source password manager available in all popular platforms: Windows, Mac, Linux, Android, F-Droid, iOS. Its free option features unlimited entries, unlike Lastpass and Dashlane counterparts. It is open-source with a self-hosting option.

I would like to tell you some background of how I stored passwords in Bitwarden. Since everything is stored on their server, I don't 100% trust them and I add salt to every of my password. For example, I store abcde as a website's password on the Bitwarden server but the actual password is abcde123noob, '123noob' is an additional character that I add to every password. That gives me peace of mind against a hacked server or malicious updates.

## Pass: Introduction

pass is a very simple password manager that follows UNIX philosophy: do one thing and do it well. It is actually a simple wrapper around PGP encryption. Being Git compatible, I can store his passwords on any Git hosting platform (I choose GitLab private repository). It has many clients available on common platforms just like Bitwarden but you need to be extra careful since independent developers develop them. You can try to look at issues opening/closed if there are obvious red flags or not.

It has excellent support on Linux with nice integration with dmenu that I am using. As for how to set it up you can look at the [website](https://www.passwordstore.org/) for details. You only need PGP-compliant private key (I am using GPG), a Git repository on a server, and in some cases, SSH keys as well.

## Why do I Prefer Pass over Bitwarden

- pass has a code size (using cloc utility on 2021-01-07) of about 3000 lines of code (mostly Bash script) while Bitwarden browser extension has more than 10000 lines of code (typescript + HTML + JS). I am more familiar with Typescript than Bash but I prefer something more minimal.
- pass gives me more control to choose the server while Bitwarden uses its own server. The self-hosted option from Bitwarden exists which can be an excellent choice for some but it costs some time and money to do so. This is not the case for pass where I can host files for free on e.g. GitLab.
