# Security

**Security Considerations During Development**
This document highlights the security considerations that should be made while developing systems; **not necessarily the security of the system being developed itself**; but things that can be done (by mistake) to expose security of the entire system or raise privacy concerns.

The following are the recommended security considerations that should be made while developing:

- **Environment Variables:** _Environment variables should not be exposed passed the development envrionment_. It is very common pitfall for developers to forget and **commit configuration files** (eg. `.env`) in their repositories. By having a configuration file for environment variable is [a good security consideration already](../Twelve%20Factor%20App#3---configuration), but committing this in the repository undoes all the gains. Include such in the `.gitignore` file.
- **Personal Data:** for the sake of privacy, do not commit data files with people's personal data like emails, phone numbers, etc. Will be good to put this files in a directory like `/data` and include it in the `.gitignore` file.
- **Wrongly Committed files:** if it happens that you have accidentally committed any of the prohibited files:
  - You can uncommit if you have not yet pushed upstream, that should be easy.
  - If you've pushed upstream, it gets a little complex, you can only now obscure by ammending the commit and doing a `--hard` push.
  - For other scenarios, [check the documentation here](https://sethrobertson.github.io/GitFixUm/fixup.html).