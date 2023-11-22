---
title: GitHub
category: "ext"
---

The `github` command access the GitHub API to query information about repositories - in particular

* code scanning results,
* Dependabot alerts, and
* secret scanning alerts.

Like the `gh` CLI, it supports environment variables, such as `GH_HOST`, `GH_OWNER`, `GH_REPO`, etc.

> Note that the GitHub command is considered experimental and currently supports approximately 50&nbsp;API&nbsp;endpoints.
> In case you encounter any issues, please report a bug report.

### Examples {#github-examples}


```shell
$ heimdall github code-scanning alerts-for-repo --repo java-playground
```
```json
[                                                                                                                                                                                            
  {                                                                                                                                                                                          
    "number": 136,                                                                                                                                                                           
    "rule": {                                                                                                                                                                                
      "id": "java/call-to-object-tostring",                                                                                                                                                  
      "severity": "note",                                                                                                                                                                    
      "description": "Use of default toString()",                                                                                                                                            
      "name": "java/call-to-object-tostring",                                                                                                                                                
      "tags": [                                                                                                                                                                              
        "maintainability",                                                                                                                                                                   
        "reliability"                                                                                                                                                                        
      ]                                                                                                                                                                                      
    },                                                                                                                                                                                       
    "tool": {                                                                                                                                                                                
      "name": "CodeQL",                                                                                                                                                                      
      "version": "2.13.4"                                                                                                                                                                    
    },                                                                                                                                                                                       
    "state": "open",
    /* ... */
  }
]
```
