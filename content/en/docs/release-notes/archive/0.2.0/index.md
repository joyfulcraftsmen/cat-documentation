---
title: "CAT 0.2.0"
description: ""
lead: ""
date: 2023-03-17T23:01:11+01:00
lastmod: 2023-03-17T23:01:11+01:00
draft: false
images: []
weight: 9996
toc: true
---

What's new?

## Tolerance in Percents

You can now set tolerance in percents and don't care about absolute numbers.

There are two values that are being compared. So, what is the base for calculate the percentage? In CAT it is always the *lower* of the two numbers. This is probably a minor thing (the values are supposed to be almost the same anyway), but it's good to know about that.

You provide the value in percents (don't divide by 100 yourself, CAT will do).



Example project that uses tolerance in percents:

```yaml
Data Sources:
- Provider: Yaml@1
  Connection string: MyProject.cat.yaml
  Name: ProjectFile

Tests:
- Name: Within percent tolerance
  Order: 1
  Description: This should pass, as the difference is within percent tolerance
  First Data Source: ProjectFile
  First Query: /Expected
  Second Data Source: ProjectFile
  Second Query: /Actual
  Expectation: SetsMatch
  Tolerance: 1.5 # this means 1.5 % (so it is exactly 1.5 when the base is 100)
  Tolerance mode: percent

- Name: Outside percent tolerance
  Order: 2
  Description: This should fail, as the difference is within outside defined tolerance
  First Data Source: ProjectFile
  First Query: /Expected
  Second Data Source: ProjectFile
  Second Query: /Actual
  Expectation: SetsMatch
  Tolerance: 0.0001
  Tolerance mode: percent

- Name: Tolerance in percent the same as difference
  Order: 3
  Description: This should pass, as the difference is the same as defined percent tolerance
  First Data Source: ProjectFile
  First Query: /Expected
  Second Data Source: ProjectFile
  Second Query: /Actual
  Expectation: SetsMatch
  Tolerance: 1 # difference is 10, which is exactly one percent of the lower value
  Tolerance mode: percent

Expected:
- Year: 2021
  Profit: 1000 # ten less than in the second set
- Year: 2022
  Profit: 1500

Actual:
- Year: 2021
  Profit: 1010 # ten more than in the first set
- Year: 2022
  Profit: 1500
```

## TRX Output Fixed

The TRX file output (see release notes for 0.1.0) was not recognized correctly in  Azure DevOps. Now it is formatted correctly and you can see nice charts after publishing test results in Azure DevOps pipeline (tested).


## Timeouts

Timeouts were missing in CAT completely. Now you can use them again. Compared to JC.Unit, they will now work with any test and any provider, even those that do not use relational databases.

--------
Thats' all for now, stay tuned ;)

