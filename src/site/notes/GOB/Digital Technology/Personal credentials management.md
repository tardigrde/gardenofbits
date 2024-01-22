---
{"dg-publish":true,"dg-path":"Gardens/Digital Technology/Personal credentials management.md","permalink":"/gardens/digital-technology/personal-credentials-management/","tags":["tech","password","credentials"],"noteIcon":1,"created":"2023-10-13","updated":"2023-10-13"}
---

# Personal credentials management

In Web 2, the internet of today, you need to authenticate yourself (and be authorized) to perform certain user flows. Most services require you to have a user account, which usually means you provide at least your email address and a password which you will use for authentication. The problem: emails and passwords are easily compromised. In 2023 even private keys are considered as a security risk. Well, not because of them being cracked, it's more about storage. They can be breached. Same with passwords, but passwords are way easier to crack too.

I know a lot of people who do not really care about their passwords. Some of them are aware that they should be pick a password that is hard to crack, and that it should be rotated frequently, but they don't do it. Most of them use some variations of their master password. They have maybe 2-4 variations, and use them across platforms. They might have it

SSO is another reason they do not care about their credentials. In some ways, SSO does help. They can just login to applications with their Google or Apple ID. Sure enough, that works for most people. But it's far from optimal, and SSO also has it's weaknesses. But that's another story.

## Local credentials management

I for instance, do care about my digital legacy, and credentials is of high priority. They pose a huge security risk on me, and I want to proactively deal with this risk.

I have tried and tested many password management tools, including Firefox Passwords, Google Passwords, iCloud Keychain and Lastpass. Some of them is very handy. Lastpass for example works across your devices, regardless of platform. But every now and then it gets [hacked](https://krebsonsecurity.com/2023/09/experts-fear-crooks-are-cracking-keys-stolen-in-lastpass-breach/#:~:text=In%20November%202022%2C%20the%20password,more%20than%2025%20million%20users.). So it got clear for me that I must take the responsibility for my passwords.

I've been using [KeePassXC Password Manager](https://keepassxc.org/) for almost 5 years now. It's free, [[GOB/Digital Technology/Open Source\|Open Source]], local-first, tracker, and ad-free! It has many useful features. There is even a [KeePassXC-Browser Extension](https://chrome.google.com/webstore/detail/keepassxc-browser/oboonakemofpalcgghocfoadofidjkkk)for auto-fill. There are mobile apps too, that work nicely with KeepassXC databases, like [Strongbox](https://strongboxsafe.com/).

I never upload my `kdbx` file to the internet, and I keep copies of it which I update frequently. I am unaware of 95% of my passwords. They are all generated and in most cases I have only seen them once, in plan text, when they were generated. 

## Password creation methods

### Generated long passwords
For services that you use rarely, just generate a long (>32 characters) password with lower and uppercase letters, numbers and **special characters**.

### Use diceware

[Diceware](https://en.wikipedia.org/wiki/Diceware) is a very cool password generation method. You use ordinary dice to generate 5 numbers, which will correspond to a word from the [Diceware word list](https://theworld.com/~reinhold/diceware.wordlist.asc). You do this [at least 7 times](https://arstechnica.com/information-technology/2014/03/diceware-passwords-now-need-six-random-words-to-thwart-hackers/) and you got yourself a memorable (if it's not, try to create a story of the words that will help you remember). When you have 7 words in your passphrase any attacker who has to guess your words, has a **one in 1,719,070,799,748,422,591,028,658,176** chance of getting it right at each guess.

See this nice [Diceware FAQ](https://theworld.com/~reinhold/dicewarefaq.html) to go deeper in the rabbit whole.

