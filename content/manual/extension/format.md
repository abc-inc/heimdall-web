---
title: Format
category: "ext"
---

The `format` command is a convenient option to convert JSON or key-value pairs to various output formats.

### Examples {#format-examples}

#### Format Properties as Table with Fixed-Width Columns

```shell
$ heimdall format --output table <./gradle/wrapper/gradle-wrapper.properties
```
```properties
org.gradle.parallel           true
org.gradle.daemon.idletimeout 1800000
```

#### Format Key-Value Pairs as JSON/YAML/etc.

```shell
$ env | grep -E '^HOMEBREW_' | heimdall format --output jsonc
```
```json
{
  "HOMEBREW_CELLAR": "/opt/homebrew/Cellar",
  "HOMEBREW_PREFIX": "/opt/homebrew",
  "HOMEBREW_REPOSITORY": "/opt/homebrew/Homebrew"
}
```
