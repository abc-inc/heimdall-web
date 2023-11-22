---
title: Docker
category: "ext"
---

The `docker` command can process `Dockerfile` and `.dockerignore` files to list matching files or print instructions.
<!--more-->
There are three subcommands:

* `context`: Parses a `.dockerignore` file and lists matching files.
* `file`: Parses a `Dockerfile` and prints its instructions.
* `ignore`: Parses a `.dockerignore` file and prints its patterns.

### Examples {#docker-examples}

#### List all Docker Instructions

To parse a `Dockerfile` and print its instructions in a structured format, the `docker file` subcommand can be used.

```shell
$ heimdall docker file -f app/Dockerfile --output yamlc
```
```yaml
- cmd: FROM
  line: FROM scratch
  start_line: 1
  end_line: 1
  value:
    - scratch
- cmd: COPY
  line: COPY hello /
  start_line: 2
  end_line: 2
  value:
    - hello
    - /
- cmd: CMD
  json: true
  line: CMD ["/hello"]
  start_line: 3
  end_line: 3
  value:
    - /hello
```

#### List Base Images

The built-in filter capabilities enable to query for certain values, like the base images used to build an image:

```shell
$ heimdall docker file --output text --query "[?cmd=='FROM'] | [-1:].value"
$ # or
$ heimdall docker file --output text --jq '[.[] | select(.cmd=="FROM")][-1].value'

buildpack-deps:bullseye-scm
```

#### Assert non-root User

Similarly, the following command checks whether the `Dockerfile` contains any `USER` instructions, and whether the final user is not the root user.

```shell
$ heimdall docker file --output text \
  --query "[?cmd=='USER'] | [-1:].value[0] | [0]!='root'"
$ # or
$ heimdall docker file --output text \
  --jq '[.[] | select(.cmd=="USER")] | last | .value[0] != "root"'

true
```

#### List Docker Build Context

The `docker context` subcommand parses the `.dockerignore` file and recursively lists files, which will be sent to the build context.
The root directory is the directory containing the ignore-file.
If the `.dockerignore` file does not exist, it is treated as empty and all files are listed.

```shell
$ heimdall docker context -f ./bin/.dockerignore --output text

heimdall
```

#### List ignore-file Patterns

To parse a `.dockerignore` file and list its patterns in any structured format, use the `ignore` subcommand.

```shell
$ heimdall docker ignore -f testdata/.dockerignore --output json
```
```json
[".dockerignore",".git","Dockerfile"]
```
