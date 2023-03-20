---
title: "CAT 0.7.0"
description: ""
lead: ""
date: 2023-03-17T23:01:11+01:00
lastmod: 2023-03-17T23:01:11+01:00
draft: false
images: []
weight: 9991
toc: true
---

## Logging

Default logging level was changed `Information`. CAT is now logging not only to files (by default to `Logs` directory next to your project file), but also to standard output. That means you will receive lot's of information in the PowerShell window where you are running CAT or in the logs in Azure DevOps pipelines.

As mentioned, you'll now get this behavior by default, so just run:

```ps1
Invoke-CatProject ./CompareProdAndUat.cat.yaml
```

To get previous behavior, run

```ps1
Invoke-CatProject ./CompareProdAndUat.cat.yaml -LoggingLevel None
```

If you want to see what went wrong, including details for all failed tests, run:

```ps1
Invoke-CatProject ./CompareProdAndUat.cat.yaml -LoggingLevel Error
```

There's also Debug lelvel, but don't use it if not necessary, it producess lots of log messages. Ideally, this should be used on a single failing test only.

Loggin was redesigned significantly, in default level (Information) you now get info about what connections you loaded, how many tests from how many providers were loaded etc. E.g., in expectation `SetsMatch`, you now get info if an error occurred in first query, in second query, during initiating the query, during reading rows etc.

## Telemetry

We need to know if CAT is used and how. Telemetry is one of the ways. We collect basic information about what features of CAT are used.

**We do NOT collect things like**

* data returned by the queries
* text of queries
* connection strings
* names of tests or data sources

What we do collect:

* some CAT high-level command was invoked (such as open a project, run tests)
* what types of expectations are used (but no text details about tests)
* what providers you use (such as SqlServer, Postgres, Dax etc.)
* how many tests were run, what time they took to run

Generally NO data about what you are testing or about your machine. Rather what features of CAT

are being used.
