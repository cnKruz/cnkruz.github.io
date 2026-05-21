---
layout: post
section-type: post
has-comments: false
title: Using multiple ssh-keys on a single client
category: tech
tags: [ "ssh", "mac", "tutorial" ]
---

When working across different projects, I usually end up setting up an SSH key to interact with a Git account through the command line or a Git client. And by “usually,” I mostly mean inside my developer bubble, where this kind of setup becomes part of the routine.

In most cases, one SSH key is enough. You set it up for your work account or your personal account, ideally keeping those separate for security reasons, and that is usually all you need.

But sometimes you need to access multiple remotes, work with different organizations, or handle separate identities from the same computer. In those cases, you can set up different identities using the SSH config file and `ssh-agent`.

let us begin with the **ssh-agent** which is a tool that help you handle the ssh-key authentication session so you don't have to type your passphrase as long as you are within the same session, as a good rule of thumb remember always to end the agent session or to keep you system secure by locking it.

### Check for existing ssh-keys

there are two ways to check for existing keys and you'll need to check both, so you can benefit from the use of these tools.

first check if there are key files in your ```~/.ssh ``` file by running:

```bash
$ ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist

```

if you have keys you should see some files with filenames like ``` id_rsa ``` and ``` id_rsa.pub``` these are the private key and the public key pairs, you would use the .pub to put on the server or remote account you would connect to, the private key should never be shared or uploaded anywhere.

Let's check if the agent is running:

```bash
$ eval "$(ssh-agent -s)"
```
this will start the agent in the background if is not currently running and return the pid for it, now let's list the keys that are already added in the agent:

```bash
$ ssh-add -l
```

if there are keys on your ``` ~/.ssh``` folder that are not on the agent already we can add them, but for now let's create a new key to be added to the agent.

### Generating a new ssh-key

the ssh-keys can be stored anywhere in your system but the standard convention and for ease of use we'll use the ``` ~/.ssh ``` folder so navigate to it 

```bash
$ cd ~/.ssh
```

once in the folder, we need to generate the new key by running:

```bash
$ ssh-keygen -t rsa -C "name@personal_email.com"
```
once you run this it will prompt for a filename, make sure you add a descriptive name, the convention is ``` id_rsa_personal ```, it will also prompt for the  passphrase the key will use.

when the command is done it will create a private and public pair of files with the provided name (one .pub and one without extension) as well as print out the keys fingerprint and the randomart image for said fingerprint, if you're interested in why theres an image and how does the randomart image work, [here](https://www.dirk-loss.de/sshvis/drunken_bishop.pdf) is the paper describing how its made, for now you can add the key to your server following this instructions for [GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) or leave it for later. 


### Setup the config file

let's check if there is already a config file.

```bash
$ open ~/.ssh/config
```
this may fail if the file does not exist so you should create it.

```bash
$ touch ~/.ssh/config
```
now let's open the and modify the file by adding an entry like this for the new key

```text
# Personal account
Host github_personal
HostName github.com  
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_rsa_personal
```
this breaks down like this:
- Host - the nickname you'll use for the host
- HostName - the IP address or domain of the remote server
- AddKeysToAgent - this will let the key to be added to a running agent automatically
- UseKeychain - this allow the passphrase t be stored in the user's keychain after a successful verification by the user
- IdentityFile - the location of the ssh-key file for the account

now is time to add the key to the ssh-agent

```bash
$ ssh-add -K ~/.ssh/id_rsa_personal
```
you can repeat this step for any key missing in the agent.

> [!Note]  
> On macOS Monterey (12.0) and later the ```-K``` option need to be replaced by  ```--apple-use-keychain ``` and the ```-A``` with ```--apple-load-keychain```

### Use the keys stored 

finally you can use the host saved in the config file by the nickname 

```bash
ssh github_personal
git@github_personal:[GitHub-Group]/[Project-Repo].git
```
or for cloning a repo

```bash
$ git clone git@github_personal:[GitHub-Group]/[Project-Repo].git 
```

I came across the need to hold multiple ssh-keys for the same host when cloning repos from different organizations and collaborations or private repos, this ssh-keys are not limited only to git but for any remote server that uses ssh authentication.

