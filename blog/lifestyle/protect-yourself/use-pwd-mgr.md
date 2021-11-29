# Use Passphrase and Password Manager

Humans are usually terrible at making passwords. Something like "password", "password123", "firstname" are not secure because they are guessable.

## Prefer Passphrase Over Password

A passphrase: a combination of words such as "correct horse battery staple" is considered to be much more secure because of the very high amount of entropy (randomness). Even the phrase "Tr0ub4dor&3" is not as secure as the previous mentioned passphrase, see xkcd's [password strength](https://xkcd.com/936/).

## Prefer Password Manager Over a Mere Passphrase

Imagine you use the same passphrase on every website, including banking websites. The security of well-known brands is usually top-notch but what about another random shady website you are logging using the same passphrase? If the passphrase is leaked on that website, a cracker (or an unethical hacker) can use the same credential to log in to your bank account.

The best way to cope with this is to set different passphrases for all websites but that is too much for our tiny brains. That's where a password manager comes in. It stores all passphrases for us and is usually integrated into common web browsers like Chrome and Firefox, mobile OS like android and iOS, and desktop OS. Here are some examples of password managers:

- Bitwarden
- Keepass
- [password-store](https://www.passwordstore.org/) (I am using this!)

We need to remember only one password to use a password manager though. Again I recommend using a passphrase instead.

## Can We Really Trust a Password Manager?

That is your work to do research yourself about each password manager. Or you may simply use an open-source password manager for the client-side. The server-side code (non-verifiable) doesn't matter much if it is encrypted perfectly from the client-side. A password manager like Bitwarden even allows you to host it yourself on your server. All passwords are non-reversible hashed, which means if a cracker somehow manages to get your hashed password he sill cannot get your actual passwords.
