---
title: Heimdall Scripts
category: "general"
---

A *Heimdall Script* is a declarative definition of multiple *Heimdall* commands grouped into *Tasks*.
It can be executed by using the `run` command.
<!--more-->

```yaml
version: '3'

env:
  HEIMDALL_FILE: '{{.USER_WORKING_DIR}}/tmp/*'

tasks:
  default:
    interpreter: expr
    cmds:
      - task: Load JaCoCo report
        cmd: heimdall java jacoco -f reports/jacoco/test/jacocoTestReport.csv -s >tmp/jacoco.json
        interpreter: sh
      - task: Line coverage (70%)
        cmd: line_covered / (line_covered + line_missed) > 0.7
```

A comprehensive usage guide can be found at [Taskfile Usage](https://taskfile.dev/usage/).

### Examples {#keyring-examples}

```shell
$ heimdall run -t ~/heimdall/docs/examples/java.yaml default
```
```text
task: [gradle] Check whether the executable flag is set.
task: [gradle] Check whether Gradle 6 or newer is used.
task: [gradle] Check whether outdated GWT plugins are used.
task: Task "jacoco-single-report" is up to date
task: [jacoco] Check JaCoCo code coverage threshold.
task: [jacoco] line_covered / (line_covered + line_missed) >= 0.60
task: Task "log4j-config" is up to date
task: [web.xml-file] Check that 'WebContent/WEB-INF/web.xml' uses Servlet spec 3.1 or later.
task: [web.xml-file] Check that 'WebContent/WEB-INF/web.xml' does not contain synchronous servlet filters for asynchronous servlets.
javamelody
12:34:56 ERR error="task: Failed to run task \"default\": exit status 1"
```
