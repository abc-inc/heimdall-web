---
title: Properties
category: "ext"
---

### Examples {#properties-examples}

#### Customize Output

By default, the `properties` command lists all properties.
Command line flags like `--get` can be used to retrieve only the desired values.

```shell
$ heimdall properties -f .git/config --get name,email --output text --jq '.[0]+" <"+.[1]+">"'

gschauer <gschauer.abc.inc@gmail.com>
```

#### Modify Properties File

The `properties` command even allows to override individual key-value pairs.

```shell
$ heimdall properties -f ~/.gradle/gradle.properties --output text \
  --set org.gradle.caching=true | sponge ~/.gradle/gradle.properties
```
