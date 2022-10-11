---
layout: default
title: SSH on Mac OSX
permalink: tools/unix/ssh-on-macos.html
tags: macosx ssh tools unix
---

# SSH on Mac OSX

## SSH Overview

### ssh

Secure Socket Shell (SSH) is the network protocol most often used for secure access to remote hosts and services. Whether you are running `ssh` from your terminal, accessing GitHub with the SSH protocol (`git@`), or any other myriad of uses, it is helpful to understand your configuration options to make working with SSH easier.

![ssh-agent flow](/assets/images/ssh-agent-flow.svg)

### ssh keys

Before using SSH, you will have to generate an SSH key and supply the public portion of that key to the remote host. GitHub has [step-by-step documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) for this. In short, the command is as follows.

```
$ ssh-keygen -t ed25519 -C "your_email@example.com"
```

You can use the following if your local OS does not support the Ed25519 algo.

```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

If you accept the default filename, you will end up with private and public keys respectively.

```
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Or if you used the RSA algo the files would be.
```
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

**Important:** Secure your key with a passphrase and only ever distribute your public key.

_Note:_ These files are referred to as IdentityFile(s) in SSH configuration, thus the `id_` prefix.

### ssh agent

The problem with securing your ssh key with a passphrase is that every time a new secure connection is made, the passphrase is required. To avoid having to manually enter the passphrase each time, you can add your identity file to the `ssh-agent` process. When doing so, you will enter your passphrase and thereafter, any time you make an SSH connection, the `ssh-agent` will handle secure access to the identity file without requiring the passphrase.

Most documentation for adding your identity files to the `ssh-agent` will do so using the `ssh-add` command. Here is the command to do so, but read on for Mac specific behavior. You will be prompted for the passphrase when you run this command but then no longer.
```
$ ssh-add -k ~/.ssh/id_rsa
```

To verify, you can list the keys the `ssh-agent` is aware of.
```
$ ssh-add -l
```

_Note:_ You can use the following command to remove all keys from the `ssh-agent`. This is useful if you want to test your ssh configuration.
```
$ ssh-add -D
```

### Mac Keychain Access

The problem with adding the identity files into the running `ssh-agent` process is that when it restarts, we have to repeat the process. Apple has integrated the Keychain so you have permanent storage for your identity files, making them available to the `ssh-agent` process. To add them, you will need to use a Mac specific option.
```
$ ssh-add --apple-use-keychain ~/.ssh/id_rsa
```

Once you have run the above, you will see the identity file with `ssh-add -l`. However, unless your ssh is configured correctly, once you restart the `ssh-agent` process, the files will no longer be visible. You have to explicitly tell ssh to use your Keychain. An inefficient way of doing so is to pass this option to your ssh commands.
```
ssh -o UseKeyChain=true host.example.com
```

A much better method is to setup a configuration file.

### ssh config

You can setup default ssh configuration in the file `~/.ssh/config`.
```
$ touch ~/.ssh/config
```

Add the following lines which will configure ssh to use the Mac Keychain. The IgnoreUnkown option allows your ssh config to work in other environments where the UseKeychain option is not valid.
```
IgnoreUnknown UseKeyChain
UseKeychain yes
```

_Note:_ The `IgnoreUnknown UseKeyChain` entry is optional but allows the configuration file to be valid for non-Mac environments.

At this point, if you restart `ssh-agent`, the `ssh-add -l` command will say the _agent has no identities_, yet your ssh commands will work because the `ssh-agent` will pull them from the Keychain. Additionally, you can include the following option to load the key into the agent when used. 
```
AddKeysToAgent
```

While not needed, you can preload the keys stored in the Mac Keychain to the `ssh-agent` by adding the following to your shell profile.
```
ssh-add --apple-load-keychain
```

## Further Reading

[SSH Multi Host Setup](/tools/unix/ssh-multi-host-setup.html)
