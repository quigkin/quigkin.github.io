---
layout: default
title: SSH Multi Host Setup
permalink: tools/unix/ssh-multi-host-setup.html
tags: macosx ssh tools unix
---

# SSH Multi Host Setup

## Overview

Typically you will only have one identity file per device but there are times you may have the need to manage multiple identities on the same device; for instance, you are transferring work from multiple devices to a single device. Or possibly you just want to separate your personal and work projects; for example, using one set of keys for host access at work and another set for personal github access.

This can easily be supported in your ssh configuration file: `~/.ssh/config`. (see [SSH on Mac OSX](/tools/unix/ssh-on-macos.html))

```
$ touch ~/.ssh/config
```

## Host Entries

By having multiple `Host` entries in your SSH config file you can specify different options for each. As an example, the following config allows you to use your peronsal ssh key for `github.com` access and your work ssh key for any hosts under `work-domain.com`.

```
Host github.com
  IdentityFile ~/.ssh/id_rsa_personal

Host *.work-domain.com
  IdentityFile ~/.ssh/id_rsa
```

## Host Aliases

The `Host` domain can also be an alias and a `Hostname` can be specified. This is useful if you need to use different keys for the same host.

```
Host personal
  Hostname github.com
  IdentityFile ~/.ssh/id_rsa_personal

Host work
  Hostname github.com
  IdentityFile ~/.ssh/id_rsa
```

Now you can use the Host alias for ssh access; for example, here we show testing ssh with github using the `personal` alias.

```
❯ ssh -T git@personal
Hi (name)! You've successfully authenticated, but GitHub does not provide shell access.
```

## Shared Host Configuration

There are many configuration options you may want to share and specifying a `Host *` entry will let you specify those.

```
Host *
  IgnoreUnknown UseKeyChain
  AddKeysToAgent yes
  UseKeychain yes

Host personal
  Hostname github.com
  IdentityFile ~/.ssh/id_rsa_personal

Host work
  Hostname github.com
  IdentityFile ~/.ssh/id_rsa
```

## Further Reading

[SSH on Mac OSX](/tools/unix/ssh-on-macos.html)

