---
title: SSH
category: "ext"
---

The `ssh config` command parses SSH configuration files and outputs the values for a certain host.

### Examples {#ssh-examples}

#### Print Values with Defaults as Table

To get the default values for unspecified parameters in your SSH configuration file, use the `--defaults` flag.

For example:

```properties
Host web?? www??.company.corp
  User me

Host *
  HostName gateway.company.corp

AddressFamily inet
```

The command `ssh config` subcommand parses the SSH configuration file located at `~/.ssh/config`.
The configuration files contain sections separated by `Host` specifications, and that section is only applied for hosts
that match one of the patterns given in the specification.

Thus, this command checks each `Host` section, which match the host named `web00`.
For each parameter `HostName`, `IdentityFile`, and `User`, the first obtained value will be used.
Since the first obtained value for each parameter is used,
more host-specific declarations should be given near the beginning of the file, and general defaults at the end.
The `--defaults` flag outputs the default values for all undefined parameters.
Finally, the output is formatted as a table (`--output table`) with fixed-width columns.

```shell
$ heimdall ssh config -f ~/.ssh/config --defaults --hostname web00 \
  --keys HostName,IdentityFile,User --output table | sort
```
```properties
HostName     gateway.company.corp
IdentityFile ~/.ssh/identity
User         me
```
