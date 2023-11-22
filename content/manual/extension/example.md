---
title: Example
category: "ext"
---

```shell
$ heimdall example java --no-pager
```
```yaml
version: '3'

set: [ errexit, nounset, pipefail ]
env:
  EVIDENCE_DIR: '{{.USER_WORKING_DIR}}/'
                                        
tasks:
...
```
