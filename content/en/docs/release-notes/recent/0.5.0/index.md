---
title: "CAT 0.5.0"
description: ""
lead: ""
date: 2023-03-17T23:01:11+01:00
lastmod: 2023-03-17T23:01:11+01:00
draft: false
images: []
weight: 9993
toc: true
---

## Create a new project more easily

In PowerShell module, there is a new command: `New-CatProject`. You can use it like this:

```ps1
cd D:\Testing\MyProjectName
New-CatProject MyProjectName
```

It will create a new file in your current directory (so navigate using `cd ...` first): `MyProjectName.cat.yaml` - an almost empty project file with one data source and one test.

In future we consider adding more switches, such as where to store data sources, where to store tests etc. Or may be even an interactive mode :)

## Limits introduced

Because this is still pre-release, date limit (will work until 2023-04-30) and limit for number of tests executed at one time were introduced.

When exceeded, the test will have `Inconclusive` result and the message will clearly state what's wrong.
