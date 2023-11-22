---
title: Go
category: "ext"
---

The `go mod` subcommand processes Go module files.
<!--more-->
The output returned represents an abstract `go.mod` file.

```json
{
  "Exclude": [],
  "Go": {
    "Version": "1.21"
  },
  "Module": {
    "Deprecated": "",
    "Mod": {
      "Path": "github.com/abc-inc/heimdall"
    }
  },
  "Replace": [
    {
      "New": {
        "Path": "github.com/cli/shurcooL-graphql",
        "Version": "v0.0.2"
      },
      "Old": {
        "Path": "github.com/shurcooL/graphql",
        "Version": "v0.0.0-20181231061246-d48a9a75455f"
      }
    }
  ],
  "Require": [
    {
      "Indirect": true,
      "Mod": {
        "Path": "github.com/shurcooL/graphql",
        "Version": "v0.0.0-20220606043923-3cf50f8a0a29"
      }
    }
    /* ... */
  ],
  "Retract": null,
  "Toolchain": {
    "Name": "go1.21.0"
  }
}
```

### Examples {#go-examples}

#### Print Go Version of Module

```shell
$ heimdall go mod --output text --query "Go.Version"

1.21
```

#### List Required Dependencies and Version

```shell
$ heimdall go mod --output text --jq \
  '[.Require[].Mod | select(.Path | test("/charmbracelet/")) | flatten | join("@")] | sort | .[]'

github.com/charmbracelet/bubbles@v0.16.1
github.com/charmbracelet/bubbletea@v0.24.2
github.com/charmbracelet/glamour@v0.6.1-0.20230531150759-6d5b52861a9d
github.com/charmbracelet/gum@v0.11.0
github.com/charmbracelet/harmonica@v0.2.0
github.com/charmbracelet/lipgloss@v0.8.0
```
