---
title: Java
category: "ext"
---

The `java` command can process all kinds of Java-related files.
It can parse and aggregate Java code coverage reports, JUnit test reports,
scan the filesystem for log4j vulnerabilities, parse a Maven POM file, and process `web.xml` files.

There are five subcommands:

* `jacoco`: Parses and aggregates Java code coverage reports.
* `junit`: Parses and aggregates JUnit test reports.
* `log4j`: Scans the filesystem for log4j vulnerabilities.
* `maven`: Parses a Maven POM file.
* `webxml`: Processes `web.xml` files.

### Examples {#java-examples}

To parse and aggregate JUnit test reports, use the `junit` subcommand. For example:

```shell
$ heimdall java junit
```

This command will parse and aggregate JUnit test reports in the current directory.

#### Total Code Coverage

```shell
$ heimdall java jacoco -f jacoco.csv --summary --output text \
  --jq '.line_covered / (.line_covered + .line_missed)'

0.94
```

#### Line Statistics of Untested Methods

First, `heimdall` parses a JaCoCo code coverage report and outputs the result in CSV format.
`xsv` - a fast CSV command line toolkit - to search within the CSV output.
It selects rows where the `method_covered` column matches the regular expression `^0$`, which means it selects rows where no methods are covered.
Then `xsv` selects only the `pkg`, `class`, and `line_missed` columns from the previous output.
Finally, `sed` effectively changes the CSV format to a tab-separated format with the `pkg` and `class` columns combined into one, separated by a dot.

```shell
$ heimdall java jacoco -f jacoco.csv --output csv |
  xsv search --select method_covered '^0$' |
  xsv select pkg,class,line_missed |
  sed -e 's/,/./' -e 's/,/\t/'
```
```text
pkg.class       line_missed
org.jacoco.core.runtime.InjectedClassRuntime.Lookup     6
org.jacoco.core.runtime.InjectedClassRuntime    16
org.jacoco.agent.rt.internal.Agent.new Thread() {...}   3
org.jacoco.agent.rt.internal.IExceptionLogger   1
org.jacoco.agent.rt.internal.Offline    8
org.jacoco.agent.rt.internal.PreMain    12
org.jacoco.agent.rt.internal.IExceptionLogger.new IExceptionLogger() {...}      3
org.jacoco.agent.rt.RT  1
com.vladium.emma.rt.RT  6
org.jacoco.examples.ExecutionDataServer 5
org.jacoco.examples.MBeanClient 12
org.jacoco.examples.ExecutionDataServer.Handler 26
org.jacoco.examples.ReportGenerator     28
org.jacoco.examples.ExecutionDataClient 13
org.jacoco.examples.CoreTutorial.TestTarget     7
```

#### Find log4j Vulnerabilities

To scan the filesystem for log4j vulnerabilities, use the `log4j` subcommand.
To get a list of files, use the `text` output mode.

```shell
$ heimdall java log4j --output text
```
```text
12:34:56 WRN error="scanning jar: failed to check JAR: opening file decoy/JndiManager.class: zip: unsupported compression algorithm" file=corrupt.jar
12:34:56 WRN error="scanning jar: failed to check JAR: checking sub jar corrupt.jar: opening file decoy/JndiManager.class: zip: unsupported compression algorithm" file=corrupt_jar_in_jar.jar
Path
arara.jar
arara.signed.jar
bad_jar_in_jar.jar
bad_jar_in_jar_in_jar.jar
bad_jar_with_invalid_jar.jar
emptydirs.zip
log4j-core-2.0-beta9.jar
log4j-core-2.1.jar
log4j-core-2.12.1.jar
log4j-core-2.14.0.jar
log4j-core-2.15.0.jar
shadow-6.1.0.jar
vuln-class.jar
```

Other output modes, like `json` or `yaml` provide all details of the scanned files.

```shell
$ heimdall java log4j ~/go/pkg/mod/github.com/google/log4jscanner\@v0.5.0/jar/testdata/ \
  --output jsonc --jq '.[] | select(.Path | test("shadow"))' 2>/dev/null
```
```json
{
  "MainClass": "",
  "Path": "shadow-6.1.0.jar",
  "Version": "",
  "Vulnerable": true,
  "Vulns": [
    {
      "CVE": "CVE-2021-44228"
    },
    {
      "CVE": "CVE-2021-45046"
    }
  ]
}
```

#### Check Servlet Specification in web.xml

The `java webxml` subcommand processes a `web.xml` file and outputs .
The `--mode raw` flag indicates that the file should be read without further processing.

The expression following command checks whether the web application deployment descriptor
uses the servlet 3.0 specification (or newer).

```shell
$ heimdall java webxml --mode raw ./web.xml --output text \
  --query 'to_number("web-app"."-version") >= `3.1`'
$ # or
$ heimdall java webxml --mode raw ./web.xml --output text \
  --jq '.["web-app"]["-version"] | tonumber >= 3'

true
```

#### Find Misconfigured Servlet Filters

The `servlet-mappings` mode lists all servlets and their configuration.
For each servlet, all matching servlet filters are listed in declaration order.
This comes in handy to determine potential configuration issues.
For example, in order to handle WebSocket requests, all matching servlet filters must support asynchronous processing.
Misconfigured servlet filters can be listed as follows:

```shell
$ heimdall java webxml --mode servlet-mappings ./web.xml --output text --jq \
  '.["servlet-mappings"][] | select(.servlet["async-supported"] == "true") | '\
  '.["filter-mappings"][].filters[] | select(["async-supported"] != "true")["filter-name"]'

javamelody
```
