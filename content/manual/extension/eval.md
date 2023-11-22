---
title: Eval
category: "ext"
---

The `eval` command provides a way to evaluate complex expressions on structured input data.
It supports different evaluation engines:

* expr: [Expr](https://github.com/antonmedv/expr) is a safe, fast, and intuitive expression evaluator optimized for the Go language.
* javascript: [JavaScript interpreter](https://github.com/dop251/goja), which implements ECMAScript 5.1.
* template: Standard [Go Template](https://pkg.go.dev/text/template) engine.

### Expr {#eval-expr}

*Heimdall* supports the same expression language, which is also used by
[Argo Rollouts and Argo Workflows](https://argoproj.github.io/) for Kubernetes.
The official [Language Definition](https://expr.medv.io/docs/Language-Definition) provides a good overview.

### Custom Functions {#eval-functions}

*Heimdall* registers more than [120 functions](https://go-task.github.io/slim-sprig/),
which can be used by any evaluation engine, which supports custom functions.

* [String Functions](https://go-task.github.io/slim-sprig/strings.html): `trim`, `plural`, etc.
    * [String List Functions](https://go-task.github.io/slim-sprig/string_slice.html): `splitList`, `sortAlpha`, etc.
* [Integer Math Functions](https://go-task.github.io/slim-sprig/math.html): `add`, `max`, `mul`, etc.
    * [Integer Array Functions](https://go-task.github.io/slim-sprig/integer_slice.html): `until`, `untilStep`, `seq`
* [Date Functions](https://go-task.github.io/slim-sprig/date.html): `now`, `date`, etc.
* [Defaults Functions](https://go-task.github.io/slim-sprig/defaults.html):
  `default`, `empty`, `coalesce`, `fromJson`, `toJson`, `toPrettyJson`, `toRawJson`, `ternary`
* [Encoding Functions](https://go-task.github.io/slim-sprig/encoding.html): `b64enc`, `b64dec`, etc.
* [Lists and List Functions](https://go-task.github.io/slim-sprig/lists.html): `list`, `first`, `uniq`, etc.
* [Dictionaries and Dict Functions](https://go-task.github.io/slim-sprig/dicts.html):
  `get`, `set`, `dict`, `hasKey`, `pluck`, `dig`, etc.
* [Type Conversion Functions](https://go-task.github.io/slim-sprig/conversion.html): `atoi`, `int64`, `toString`, etc.
* [Path and Filepath Functions](https://go-task.github.io/slim-sprig/paths.html):
  `base`, `dir`, `ext`, `clean`, `isAbs`, `osBase`, `osDir`, `osExt`, `osClean`, `osIsAbs`
* [Flow Control Functions](https://go-task.github.io/slim-sprig/flow_control.html): `fail`
* Advanced Functions
    * [OS Functions](https://go-task.github.io/slim-sprig/os.html): `env`, `expandenv`
    * [Reflection](https://go-task.github.io/slim-sprig/reflection.html): `typeOf`, `kindIs`, `typeIsLike`, etc.
    * [Cryptographic and Security Functions](https://go-task.github.io/slim-sprig/crypto.html): `sha256sum`, etc.

### Examples {#eval-examples}

#### Check that URL matches Regular Expression

The following command checks that the Gradle Wrapper defines Gradle 7 or newer:

```shell
$ heimdall eval -e 'base(distributionUrl) matches "gradle-[7-9][.]"' \
  gradle/wrapper/gradle-wrapper.properties

true
```

#### Process Multiple Files

*Heimdall* accepts any number of input files.
Non-existing files are treated as fatal errors, unless the `--ignore-missing` flag is set.
This enables *Heimdall* to evaluate expression on a set of input files.
If a variable is defined multiple times among any input files, the last definition takes precedence.

The following command reads multiple files in properties format and prints all distinct variables in lexical order.

```shell
$ heimdall eval --ignore-missing -e 'join("\n", sortAlpha(keys(_)))' \
  ${GRADLE_USER_HOME:-~/.gradle}/gradle.properties gradle.properties

org.gradle.cache.cleanup
org.gradle.caching
org.gradle.configureondemand
org.gradle.daemon.idletimeout
org.gradle.parallel
org.gradle.vfs.watch
```

#### Override File Format

The `eval` command determines the file format solely based on the file extension.
For files, which have an unknown file extension, or when reading from standard input, the type needs to be defined.
This is done with the following syntax:

```shell
$ heimdall eval -e <expr> <file1>:<prefix1>:<type1> <file2>:<prefix2>:<type2> ...
```

Both, `prefix` and `type` are optional, i.e., `file` is equivalent to `file::`.
For example, the argument `app/Dockerfile::properties` instructs *Heimdall* to read the file as a `.properties` file.
As a consequence, it defines variables, with the Docker instructions being the keys and the arguments being the values.

The following command reads two `Dockerfiles` and assigns Docker instructions to variables prefixed with `a` and `w`, respectively.
It uses the default evaluation engine *Expr* to check whether both `Dockerfile` use the same base image,
and the web container exposes port 8080.

```shell
$ heimdall eval -e "a.FROM == w.FROM" -e "int(w.EXPOSE) == 8080" \
  testdata/Dockerfile:a:properties testdata/Dockerfile:w:properties

true
true
```

#### Read from Standard Input

```shell
$ heimdall java jacoco --summary jacoco.csv |
  heimdall eval -E javascript \
  -e 'line_covered / (line_covered + line_missed)' -- -::json

true
```

First, *Heimdall* reads a JaCoCo code coverage report in CSV format and produces a summary in JSON format.
The output is fed into the JavaScript interpreter to calculate coverage ratio.
Note the `-::json`, which means: read standard input (`-`), use no variable prefix, and treat the input as JSON.
