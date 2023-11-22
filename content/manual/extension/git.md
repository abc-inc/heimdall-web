---
title: Git
category: "ext"
---

The `git commits` subcommand lists the commits between two arbitrary commits specified by `--start-ref` and `--end-ref`, respectively.

Refs can be
 
* `HEAD`, `refs/remotes/origin/HEAD`
* `branch`, `heads/branch`, `refs/heads/branch`, `refs/remotes/origin/branch`
* `tag`, `refs/tags/tag`
* tilde and caret (`HEAD~1`, `master~^`, `tag~2`, `ref/heads/master~1`, ...)
* selection by text (`HEAD^{/fix nasty bug}`)
* hash (prefix and full)

The optional `--merge-base` flag finds the best common ancestor between those two commits and adjusts the starting ref accordingly.

### Examples {#git-examples}

```shell
$ heimdall git commits --merge-base --start-ref 7b2b24a --end-ref eb742d2 \
  ~/Documents/musikcube/ --output text --jq \
  '.[] | [.Author.When, .Author.Name, .Message | sub("\n.*"; ""; "g") | sub("(?<x>.{70}).+"; "\(.x)...")] | join("\t")'
```
```text
2021-12-27T15:11:05-08:00       casey langen    CHANGELOG update.
2021-12-27T15:10:36-08:00       casey langen    Bump commit hash for release.
2021-12-27T13:21:17-08:00       casey langen    Refactored and added logging to SystemService to improve notification ...
2021-12-27T13:20:56-08:00       casey langen    Update Android Studio and dependencies.
2021-12-22T10:24:37-08:00       casey langen    Merge pull request #479 from ravensiris/master
2021-12-22T08:45:46+01:00       Maksymilian Jodłowski   Add missing header.
2021-12-18T21:09:37-08:00       casey langen    Merge pull request #475 from gschauer/master
2021-12-18T21:08:00-08:00       casey langen    Fixed notification tray icon for windows builds.
2021-12-08T14:43:49Z    gschauer        Add CircleCI build for Debian Bullseye
2021-12-07T22:47:28Z    gschauer        Fix dependencies for Ubuntu Impish
2021-11-18T13:15:44-08:00       casey langen    Fix mint build in circle ci script.
```
