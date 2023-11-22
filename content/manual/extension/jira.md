---
title: Jira
category: "ext"
---

### Examples {#jira-examples}

#### Top 10 Open Bugs

List the top 10 open bugs and return all details (including nested fields) about assignee, priority and summary.

```shell
$ heimdall jira issue -q "project = ABC AND type = Bug AND resolution = Unresolved ORDER BY priority DESC, updated DESC" \
  --fields 'assignee,priority,summary' --max-results 10
```

#### Release Notes as Custom JSON

List all stories and output a custom formatted JSON for summarizing the release notes.

```shell
$ heimdall jira issue -q "project = ABC AND type = Story AND fixVersion='ABC 1.2' ORDER BY id" \
  --output jsonc --jq '[.[] | {id: .key, summary: .fields.summary, status: .fields.status.name}]'
```
