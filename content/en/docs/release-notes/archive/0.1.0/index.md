---
title: "CAT 0.1.0"
description: ""
lead: ""
date: 2023-03-17T23:01:11+01:00
lastmod: 2023-03-17T23:01:11+01:00
draft: false
images: []
weight: 9997
toc: true
---

Things changed a bit, read carefully please...

{{< alert icon="❗❗" context="danger" >}}
The current version is unfortunatelly compatible only with PowerShell 7 (will be addressed). Please try out CAT on [PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.3#msi).
{{< /alert >}}

## Versioning

Why has this version number **0.1.0**? We use [Semantic versioning](https://semver.org) - and in this release there is new functionality (basic output handling, such as renaming TRX files).

## Things are renamed

What got renamed? Basically everything:

 JC.Unit                  | CAT
--------------------------|-------------------------------------------
Connection / Source       | Data Source
Connection Type           | Provider
TestDescription           | Description
TestQuerySource         | First Data Source *(or just Data Source)*
TestQuery                | First Query *(or just Query)*
ReferenceQuerySource    | Second Data Source
ReferenceQuery           | Second Query
TestTyppe                | Expectation
ZeroRows                  | SetIsEmpty
SameData                  | SetsMatch
RowCount                  | SetRowCount

For naming of tests you can use either:

* TestSuite, Order, TestCase, TestName
* or just Suite, Order TestCase, Name

If a name is composed of more words (such as First Query), all these forms are allowed:

* FirstQuery
* First_Query
* First Query

*(with underscore, space, or without anything)*

### Example of a Project File with New Naming Conventions

```yaml
Data Sources:

- Name: YamlData
  Provider: Yaml@1
  Connection String: ./Employees.yaml

- Name: CsvData
  Provider: Csv@1
  ConnectionString: . # current folder, where this project file is

- Name: CsvOleDBData
  Provider: CsvOleDB@1 # this allows more advanced SQL (but requires driver)
  ConnectionString: .
  ExtendedProperties: Text;HDR=YES;FMT=Delimited;characterset=65001;

Tests:

- Name: Both files contain the same data
  Order: 1
  Description: This should pass, the data are the same
  First Data Source: YamlData
  First Query: /Employees
  Second Data Source: CsvData
  Second Query: SELECT * FROM Employees.csv
  Expectation: SetsMatch

- Name: Different data
  Order: 2
  Description: This should fail, we changed order in the second set (order matters!)
  First Data Source: YamlData
  First Query: /Employees
  Second Data Source: CsvOleDBData
  Second Query: SELECT * FROM Employees.csv ORDER BY ID DESC
  Expectation: SetsMatch

- Name: More rows in first set
  Order: 3
  Description: This should fail, we have only one row in the second set (the first row is the same in both)
  First Data Source: YamlData
  First Query: /Employees
  Second Data Source: CsvOleDBData
  Second Query: SELECT * FROM Employees.csv WHERE ID = 1
  Expectation: SetsMatch

- Name: More rows in second set
  Order: 4
  Description: This should fail, we have only two rows in the first set, in the second set 4 (they are duplicated using union all)
  First Data Source: YamlData
  First Query: /Employees
  Second Data Source: CsvOleDBData
  Second Query: SELECT * FROM Employees.csv UNION ALL SELECT * FROM Employees.csv
  Expectation: SetsMatch
```

For more examples, see the [Pester tests](https://dev.azure.com/joyfulcraftsmen/Products/_git/jc-cat?path=/Tests/IntegrationTests&version=GBrelease/0-1-0&_a=contents) (ask for read access if you don't have it). It is full of examples how to use the project files.

### Compatibility with JC.Unit

The naming conventions from JC.Unit are not supported, but here is a query that helps you at least with the assets defined in the database:

```sql
-- for data sources
SELECT  cd.ConnectionName as Name, cd.ConnectionType + '@1' as Provider, cd.ConnectionString
FROM    Test.ConnectionDefinition as cd
```

```sql {linenos=table,hl_lines=[8,"15-17"],linenostart=199}
-- for tests
SELECT t.TestSuite,
       t.[Order],
       t.TestCase,
       t.TestName,
       t.TestDescription AS [Description],
       t.TestQuerySource AS [FirstDataSource],
       t.TestQuery AS [FirstQuery],
       t.ReferenceQuerySource AS [SecondDataSource],
       t.ReferenceQuery AS [SecondQuery],
       CASE t.TestTypeName
           WHEN 'SameData' THEN
               'SetsMatch'
           WHEN 'ZeroRows' THEN
               'SetIsEmpty'
           WHEN 'Rowcount' THEN
               'SetRowCount'
           ELSE
               t.TestTypeName
       END AS [Expectation],
       t.Categories,
       t.Tolerance,
       t.Timeout
FROM Test.TestDefinition AS t;
```

## Default Location for Data Sources and Tests is the Project File

From this version onwards, you don't have to specify where your data sources and test are - by default, they are expected in the project file. So in the project file (e.g., BigBang.cat.yaml), just provide them:

```yaml
Data Sources:
- .....
- .....

Tests:
- .....
- .....
- .....
```

See the example above.

If your tests or data sources are elswhere (not in the project file), you can specify where they are like this:

```yaml
Get list of data sources from:
- Provider: SqlServer@1
  Connection string: data source=localhost;integrated security=true;initial catalog=BigBang;
  Query: SELECT * FROM Tests.ConnectionDefinition

Get list of tests from:
- Provider: SqlServer@1
  Connection string: data source=localhost;integrated security=true;initial catalog=BigBang;
  Query: SELECT * FROM Tests.TestDefinition
```

And that's it. Similarly for Excel, ORACLE, YAML file (if you have tests in a separate file), or any other provider. For databases, the query can be a stored procedure, e.g. `Query: EXEC Tests.GetSmokeTests @UseFilter=0;`

## Handling Output

This is still experimental feature, but already works. So far only TRX, YAML and JSON formats of output are supported. TRX (not yet tested) complies with the format expected by Azure DevOps, but contains less information. YAML and JSON contain all information about the test (complete test definition, all information about the exectuion, detailed exception).

In the project file, just use:

```yaml
Output: trx
```

You can ask for more types of output:

```yaml
Output: trx, yaml, json
```

It will generate the file in the `TestResults` folder next to your project file.

If you need more control on where the file is and how it is named, use this syntax:

```yaml
Output:
- Format: trx
  Path: LatestResults.trx
- Format: json
  Path: result-{timestamp}.json
```

The {timestamp} will be replaced with current date/time that corresponds to the end of the tests execution (basically the point in time when the last test was executed, that means before the output is processed).

## CAT testing

Last but not least, progress was made with unit testing and integration testing of the CAT solution - at least it started :)


## Limitations

* Still no GUI
* Still no DevOps extension (comming soon)
* Works with [PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.3#msi) only (will be addressed)
* Still may be buggy (but CAT will be soon covered with lots of automated tests)



