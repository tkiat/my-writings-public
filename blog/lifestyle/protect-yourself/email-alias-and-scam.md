# Email Aliases and Scam Potential

As a Tunanota user, I am curious why they don't allow plus sign alias. After searching I came across a [post](https://www.reddit.com/r/tutanota/comments/g2gq6j/when_will_signs_in_email_addresses_allowed/fno1ine?utm_source=share&utm_medium=web2x&context=3) from a Tutanota Reddit moderator in which he links to this [post](https://jameshfisher.com/2018/04/07/the-dots-do-matter-how-to-scam-a-gmail-user/) on why dots don't matter policy of Google can create an attack vector. This article is based on that post.

## Dots don't Matter in Gmail Addresses

```text
If someone accidentally adds dots to your address when emailing you, you'll still get that email. For example, if your email is johndoe@gmail.com, you own all dotted versions of your address:

john.doe@gmail.com
jo.hn.do.e@gmail.com
j.o.h.n.d.o.e@gmail.com
```

<center>Taken from <a href="https://support.google.com/mail/answer/7436150?hl=en">Google support</a></center>

## How Email Aliases Can Create an Attack Vector

Consider this scenario of attack (adapted from [here](https://jameshfisher.com/2018/04/07/the-dots-do-matter-how-to-scam-a-gmail-user/)).

1. Try to enter a random Gmail address on a signup form until you find a registered one. Let's say johndoe<span>@</span>gmail.com.
2. Create a Netflix account with john.doe<span>@</span>gmail.com and sign up for a free trial with a throwaway card number
3. Hope Netflix will someday send an email to john.doe<span>@</span>gmail.com asking for a valid card and he also uses Netflix.
4. After a real john.doe<span>@</span>gmail.com owner enters his card number, change Netflix account email address. This will prevent him to access this account.

This scenario also applies to plus (+) alias.
