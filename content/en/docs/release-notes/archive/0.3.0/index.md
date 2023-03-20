---
title: "CAT 0.3.0"
description: ""
lead: ""
date: 2023-03-17T23:01:11+01:00
lastmod: 2023-03-17T23:01:11+01:00
draft: false
images: []
weight: 9995
toc: true
---

## PowerShell Module

PowerShell module has now more commands.

```ps1
Get-Command -Module CAT
```

Most important new one is `Invoke-CatProject`. It is a short hand for

* opening the project and loading data sources and tests
* invoking all tests
* display nicely formatted summary.

**PowerShell module now finally displays progress durring the test execution.**.

So in the basic scenario, when you always need to re-read the .cat.yaml project, run all tests and see formatted results, just use

```ps1
Invoke-CatProject ./MyProject.cat.yaml
```

or just  without any parameters, if you are in a directory with exactly one project file:

```ps1
Invoke-CatProject
```

## Logging

See documentation here: [Logging](../../Reference/Logging/Logging.md)

