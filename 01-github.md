---
title: Remotes in GitHub
teaching: 45
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Set up a GitHub account
- And hook up your computer to it

::::::::::::::::::::::::::::::::::::::::::::::::::

## 0\. Create a GitHub account

You don't have to use [GitHub](https://github.com) to share your code. But why take chances?

1. Go to ```https://github.com/```
1. Sign up using your rice gmail, or another secure account
1. And then go to your homepage on GitHub.com
1. Add a picture of your favorite rabbit to your profile. This is key to good coding.

![Github Profile: https://github.com/JohnMulligan](fig/robot.png)

## 1\. Create a remote repository

Now that you're logged into to [GitHub](https://github.com), click on the icon in the top right corner to
create a new repository called `planets`:

![](fig/github-create-repo-01.png){alt='Creating a Repository on GitHub (Step 1)'}

Name your repository "planets" and then click "Create Repository".

Note: Check "Initialize this repository with a README," and use "Add .gitignore" and "Add a license."

![](fig/github-create-repo-02.png){alt='Creating a Repository on GitHub (Step 2)'}

As soon as the repository is created, GitHub displays a page with a URL and some
information on how to configure your local repository:

![](fig/github-create-repo-03.png){alt='Creating a Repository on GitHub (Step 3)'}

## 2\. Connect your local system to the remote repository

The home page of the repository on GitHub includes the URL string we need to identify it:

![](fig/github-find-repo-string.png){alt='Where to Find Repository URL on GitHub'}

Click on the 'SSH' link to change the [protocol](../learners/reference.md#protocol) from HTTPS to SSH.

:::::::::::::::::::::::::::::::::::::::::  callout

## HTTPS vs. SSH

We use SSH here because, while it requires some additional configuration, it is a security protocol widely used by many applications.  The steps below describe SSH at a minimum level for GitHub. A supplemental episode to this lesson discusses advanced setup and concepts of SSH and key pairs, and other material supplemental to git related SSH. 

::::::::::::::::::::::::::::::::::::::::::::::::::

Before Dracula can connect to a remote repository, he needs to set up a way for his computer to authenticate with GitHub so it knows it's him trying to connect to his remote repository.

We are going to set up the method that is commonly used by many different services to authenticate access on the command line.  This method is called Secure Shell Protocol (SSH).  SSH is a cryptographic network protocol that allows secure communication between computers using an otherwise insecure network.

SSH uses what is called a key pair. This is two keys that work together to validate access. One key is publicly known and called the public key, and the other key called the private key is kept private. Very descriptive names.

You can think of the public key as a padlock, and only you have the key (the private key) to open it. You use the public key where you want a secure method of communication, such as your GitHub account.  You give this padlock, or public key, to GitHub and say "lock the communications to my account with this so that only computers that have my private key can unlock communications and send git commands as my GitHub account."

What we will do now is the minimum required to set up the SSH keys and add the public key to a GitHub account.

The first thing we are going to do is check if this has already been done on the computer you're on.  Because generally speaking, this setup only needs to happen once and then you can forget about it.

:::::::::::::::::::::::::::::::::::::::::  callout

## Keeping your keys secure

You shouldn't really forget about your SSH keys, since they keep your account secure. It's good
practice to audit your secure shell keys every so often. Especially if you are using multiple
computers to access your account.


::::::::::::::::::::::::::::::::::::::::::::::::::

We will run the list command to check what key pairs already exist on your computer.

```bash
ls -al ~/.ssh
```

Your output is going to look a little different depending on whether or not SSH has ever been set up on the computer you are using.

Dracula has not set up SSH on his computer, so his output is

```output
ls: cannot access '/c/Users/Vlad Dracula/.ssh': No such file or directory
```

If SSH has been set up on the computer you're using, the public and private key pairs will be listed. The file names are either `id_ed25519`/`id_ed25519.pub` or `id_rsa`/`id_rsa.pub` depending on how the key pairs were set up.  
Since they don't exist on Dracula's computer, he uses this command to create them.

### 3\.1 Create an SSH key pair

To create an SSH key pair Vlad uses this command, where the `-t` option specifies which type of algorithm to use and `-C` attaches a comment to the key (here, Vlad's email):

```bash
$ ssh-keygen -t ed25519 -C "vlad@tran.sylvan.ia"
```

If you are using a legacy system that doesn't support the Ed25519 algorithm, use:
`$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

```output
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/Vlad Dracula/.ssh/id_ed25519):
```

We want to use the default file, so just press <kbd>Enter</kbd>.

```output
Created directory '/c/Users/Vlad Dracula/.ssh'.
Enter passphrase (empty for no passphrase):
```

Now, it is prompting Dracula for a passphrase.  Since he is using his lab's laptop that other people sometimes have access to, he wants to create a passphrase.  Be sure to use something memorable or save your passphrase somewhere, as there is no "reset my password" option.

```output
Enter same passphrase again:
```

After entering the same passphrase a second time, we receive the confirmation

```output
Your identification has been saved in /c/Users/Vlad Dracula/.ssh/id_ed25519
Your public key has been saved in /c/Users/Vlad Dracula/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:SMSPIStNyA00KPxuYu94KpZgRAYjgt9g4BA4kFy3g1o vlad@tran.sylvan.ia
The key's randomart image is:
+--[ED25519 256]--+
|^B== o.          |
|%*=.*.+          |
|+=.E =.+         |
| .=.+.o..        |
|....  . S        |
|.+ o             |
|+ =              |
|.o.o             |
|oo+.             |
+----[SHA256]-----+
```

The "identification" is actually the private key. You should never share it.  The public key is appropriately named.  The "key fingerprint"
is a shorter version of a public key.

Now that we have generated the SSH keys, we will find the SSH files when we check.

```bash
ls -al ~/.ssh
```

```output
drwxr-xr-x 1 Vlad Dracula 197121   0 Jul 16 14:48 ./
drwxr-xr-x 1 Vlad Dracula 197121   0 Jul 16 14:48 ../
-rw-r--r-- 1 Vlad Dracula 197121 419 Jul 16 14:48 id_ed25519
-rw-r--r-- 1 Vlad Dracula 197121 106 Jul 16 14:48 id_ed25519.pub
```

### 3\.2 Copy the public key to GitHub

Now we have a SSH key pair and we can run this command to check if GitHub can read our authentication.

```bash
ssh -T git@github.com
```

```output
The authenticity of host 'github.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
git@github.com: Permission denied (publickey).
```

Right, we forgot that we need to give GitHub our public key!

First, we need to copy the public key.  Be sure to include the `.pub` at the end, otherwise you're looking at the private key.

```bash
cat ~/.ssh/id_ed25519.pub
```

```output
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDmRA3d51X0uu9wXek559gfn6UFNF69yZjChyBIU2qKI vlad@tran.sylvan.ia
```

Now, going to GitHub.com, click on your profile icon in the top right corner to get the drop-down menu.  Click "Settings," then on the
settings page, click "SSH and GPG keys," on the left side "Account settings" menu.  Click the "New SSH key" button on the right side. Now,
you can add the title (Dracula uses the title "Vlad's Lab Laptop" so he can remember where the original key pair
files are located), paste your SSH key into the field, and click the "Add SSH key" to complete the setup.

Good! We are now ready to connect our local system to this remote codebase!

### 4\. Pull down your code.

Copy that URL from the browser, go into the local `planets` repository, and run
this command:

```bash
$ git clone git@github.com:vlad/planets.git
```

Make sure to use the URL for your repository rather than Vlad's: the only
difference should be your username instead of `vlad`.

    Cloning into 'planets2'...
    remote: Enumerating objects: 5, done.
    remote: Counting objects: 100% (5/5), done.
    remote: Compressing objects: 100% (4/4), done.
    Receiving objects: 100% (5/5), 5.98 KiB | 5.98 MiB/s, done.
    remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0

We can now check to see that the repository has been cloned:

    ls .

And we can then navigate into the folder and take a look at what's been copied.

    cd planets2
    ls .

Inspect that folder in your text editor or filebrowser. We can also check this repository's connection to the remote repo.

```bash
$ git remote -v
```

```output
origin   git@github.com:vlad/planets.git (fetch)
origin   git@github.com:vlad/planets.git (push)
```

### 5\. Push a change

Add a blank file named after your favorite rabbit:

    touch rabbit.txt
    
You should see that file in your filebrowser. Now type the following:

    git add .
    git commit -m "hello rabbit"
    git push

Refresh your browser window. What do you see?

In the next lesson, we'll figure out what all of this is for :)

:::::::::::::::::::::::::::::::::::::::: keypoints

- A local Git repository can be connected to one or more remote repositories.
- Use the SSH protocol to connect to remote repositories.
- `git push` copies changes from a local repository to a remote repository.
- `git pull` copies changes from a remote repository to a local repository.

::::::::::::::::::::::::::::::::::::::::::::::::::

