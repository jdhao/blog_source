---
title: "Push to GitHub with GitHub Access Token"
date: 2022-04-23T14:30:06+08:00
markup: markdown
tags: [GitHub]
categories: [Git]
---

To promote security, starting from Aug. 2021, many GitHub operations require use of personal access tokens (PAT).

<!--more-->

# Generate personal access token

Go to https://github.com/settings/tokens and click `Generate new token`. Follow the guidelines.
Usually the token will expire after the specified time, unless you make it a permanent token,
which is not advised by GitHub.

After this, when you push to your GitHub repo, the password you use will be the token generated.

# Update tokens

After an old token expires, we need to generate a new token.
Then we need to remove the old token and use the new token.
The process may vary based on your system.

## macOS

### command line

We need to erase the old token, run the following command:

```bash
security delete-internet-password -l github.com
```

Then run your git operation as usual and you will be prompted to enter your new token.
Once your new token is entered and verified, it will be updated to the Keychain app automatically.

### Keychain app

You may also directly update the token via the Keychain Access app.
Open the app and search for github.
Then open the searched item for github and directly update your access token there.

# References

+ https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/
+ update GitHub access token on mac: https://gist.github.com/jonjack/bf295d4170edeb00e96fb158f9b1ba3c
