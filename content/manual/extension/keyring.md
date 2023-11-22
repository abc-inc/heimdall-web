---
title: Keyring
category: "ext"
---

The `keyring` command can fetch secrets with the system keyring.
It supports OS X, Linux/BSD (dbus) and Windows.
<!--more-->

### Dependencies

#### OS X

The OS X implementation depends on the `/usr/bin/security` binary for
interfacing with the OS X keychain. It should be available by default.

#### Linux and *BSD

The Linux and *BSD implementation depends on the [Secret Service](https://specifications.freedesktop.org/secret-service/latest/)
dbus interface, which is provided by [GNOME Keyring](https://wiki.gnome.org/Projects/GnomeKeyring).

It's expected that the default collection `login` exists in the keyring, because it's the default in most distros.
If it doesn't exist, you can create it through the keyring frontend program [Seahorse](https://wiki.gnome.org/Apps/Seahorse):

* Open `seahorse`
* Go to **File > New > Password Keyring**
* Click **Continue**
* When asked for a name, use: **login**

### Examples {#keyring-examples}

```shell
$ heimdall keyring get --system github.com --user me

ghp_0123456789
```
