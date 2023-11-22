---
title: Commands
category: "general"
---

### Command Structure

The CLI uses a multipart structure on the command line that must be specified in this order:

1. The base call to the `heimdall` program.
2. The top-level command, which typically corresponds to a system or technology.
3. The subcommand that specifies which operation to perform.
4. General CLI options or parameters required by the operation.
   You can specify these in any order.
   If an exclusive parameter is specified multiple times, only the last value applies.

```shell
$ heimdall <command> <subcommand> [options and parameters]
```

Parameters can take various options.
What is supported is dependent upon the command and subcommand you specify.

The examples in this guide are formatted using the following conventions:

* Prompt – The command prompt uses the Linux prompt and is displayed as (`$ `).
  Do not include the prompt when you type commands.
* Directory – Paths to files or directories use the Unix file separator `/` e.g., `./app/Dockerfile`.
* Output – Output returned by *Heimdall* is shown under user input, and is formatted as `monospace output`.
