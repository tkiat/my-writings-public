# The Importance of Passphrase and Password Manager: Introduction to pass

## Why Passphrase and Password Manager?

Humans are usually terrible at making passwords, something like "password123" and "firstname" are not secure because they are guessable. On the other hand, a passphrase, a combination of words such as "correct horse battery staple", is considered to be much more secure because of the very high amount of entropy (randomness). Even the phrase "Tr0ub4dor&3" is not as secure as the previously mentioned passphrase, see xkcd's [password strength](https://xkcd.com/936/).

Imagine you use the same passphrase on every website, including banking websites. The security of well-known brands is usually top-notch but what about another random shady website you are logging using the same passphrase? If the passphrase is leaked on that website, a cracker (a.k.a an unethical hacker) can use the same credential to log in to your bank account.

The best way to prevent this is to set different passphrases for all websites but that is too much for our tiny brains. That's where a password manager comes in. It stores all passphrases for us and is usually integrated into common web browsers and operating systems. Here are some examples of password managers:

- Bitwarden
- Keepass
- [pass](https://www.passwordstore.org/) (I am using this!)

We need to remember only one password (preferably passphrase) to use a password manager though. Don't forget it!

## Can We Really Trust a Password Manager?

That is your work to do research yourself about each password manager. Or you may simply use an open-source password manager for the client-side. The server-side code (non-verifiable) doesn't matter much if it is encrypted perfectly from the client-side. A password manager like Bitwarden even allows you to host it yourself on your server. All passwords are non-reversible hashed, which means if a cracker somehow manages to get your hashed password he sill cannot get your actual passwords.

## Password Managers Overview: Bitwarden and pass

### Bitwarden: Introduction

Bitwarden is considered a very awesome open-source password manager available in all popular platforms: Windows, Mac, Linux, Android, F-Droid, iOS, etc. Its free option features unlimited entries, unlike Lastpass and Dashlane counterparts. It is open-source with a self-hosting option.

I would like to tell you how I stored passwords in Bitwarden. Since everything is stored on their server, I don't 100% trust them (paranoia?) and I add salt to each of my password. For example, I store `abcde` to Bitwarden but the actual password is `abcde123noob`, `123noob` is additional characters I add to every password. That gives me even more peace of mind against a hacked server or a malicious update.

### pass: Introduction

pass is a very simple password manager that follows the UNIX philosophy: do one thing and do it well. It is actually a simple wrapper around PGP encryption. Being Git compatible, I can store passwords on any (private) Git repository. It has many clients available on common platforms just like Bitwarden but you need to be extra careful since independent developers develop them. You can try to look at issues opening/closed if there are obvious red flags or not.

It has excellent support on Linux with nice integration with dmenu that I am using. As for how to set it up you can look at the [website](https://www.passwordstore.org/) for details. You only need PGP-compliant private key (I am using GPG), a Git repository on a server, and in some cases, SSH keys as well.

### Why do I Prefer pass Over Bitwarden

- pass has a code size (using cloc utility on 2021-01-07) of about 3000 lines of code (mostly Bash script) while Bitwarden browser extension has more than 10000 lines of code (Typescript + HTML + JS). I am more familiar with Typescript than Bash but I prefer something more minimal.
- pass gives me more control to choose the server while Bitwarden uses its own server. The self-hosted option from Bitwarden exists which can be an excellent choice for some but it costs some time and money to do so. This is not the case for pass where I can host files for free of cost on e.g. GitLab.
