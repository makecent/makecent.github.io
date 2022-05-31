---
title: Adding ssh key to Github
parent: Posts
---
1. TOC
{:toc}

# Check for existing ssh key
```shell
ls -al ~/.ssh
```

# Generate ssh key (if no key)
```shell
ssh-keygen -t ed25519 -C "luchongkai96@gmail.com"
```
Replace the email address with yours

# Adding ssh private key to ssh-agent (optional)
So you only need type the passwd once in every session.
## Run ssh-agent
```shell
eval "$(ssh-agent -s)"
```
## Add ssh private key to the running agent
```shell
ssh-add ~/.ssh/id_ed25519
```

# Adding ssh public key to GitHub account
- Copy the SSH public key to your clipboard.
```shell
cat ~/.ssh/id_ed25519.pub
```
- In the upper-right corner of any page, click your profile photo, then click Settings.
![image](https://user-images.githubusercontent.com/42603768/171116930-94665d20-96f1-46af-8789-c0afae9f21e8.png)

- In the "Access" section of the sidebar, click  SSH and GPG keys.

- Click New SSH key or Add SSH key.
![image](https://user-images.githubusercontent.com/42603768/171117477-899a696f-97bf-4527-bb84-6e3c918b1eca.png)

- In the "Title" field, add a descriptive label for the new key. For example, if you're using a personal Mac, you might call this key "Personal MacBook Air".
- Paste your key into the "Key" field.
![image](https://user-images.githubusercontent.com/42603768/171117564-082ba037-473b-4983-9214-2a3cb734896e.png)
