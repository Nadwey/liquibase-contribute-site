---
title: "Milestone 2: Fix & Retest"
---

# Fix and Retest

## Overview

When the Test Harness tests run, it will look for change and snapshot tests and expectations in your `src/test/resources/liquibase/harness` directory. 

## Finding Files

The test harness uses an algorithm of finding the "most correct" version of files for your database. These files are used for both defining what **_to test_** and also what **_to expect_**.  

The base search directory is `src/test/resources/liquibase/harness/*` with different subdirectories for each type of test. For example, the "change" tests use a `${base directory}` of `src/test/resources/liquibase/harness/change`.

When searching for a file such as `file.ext`, Test Harness checks for the following pattern:

1. `${base directory}/${db name}/${major version}/{$minor version}/file.ext`
1. `${base directory}/${db name}/${major version}/file.ext`
1. `${base directory}/${db name}/file.ext`
1. `${base directory}/file.ext`

where it uses the contents of file it finds. 

This means that when defining "expectedSql" or changelogs or expectedSnapshots or any other file test harness expects, you can override the default expectations by creating new files higher up in the pattern.

!!! tip

    Keep your files as generic as possible. If the expectedSql file works for all versions of your database, create the file in ${base directory}/${db name}/file.ext`. 

    Only use major and minor versions when it actually depends on the specific versions.  

## Golden Master Files

Many of the tests use "expected" files and will either auto-create them if they do not exist or fail until the file is created.

These files let you manually inspect the SQL being run by a particular change setup to ensure it is doing what you expect now, and will be checked into Git so that it is easy to detect any changes to this known good behavior down the road.

## Fixing Logic

For tests that are failing because how Liquibase interacts with the database needs to be customized for your database, you will create one of two types of classes:

- [New SqlGenerator implementations](../add-a-sql-generator.md) for Change -> SQL mappings that are wrong
- [New SnapshotGenerator implementations](../add-a-snapshot-generator.md) for snapshot logic that is wrong

The general pattern for both is to create a new class that returns a higher priority **_if and only if_** Liquibase is using the `Database` class you defined in [milestone 1](milestone1-step1.md).

!!! tip

    What method you implement to specify the Database implementation your new generator classes are for will depend on the specific class you are implementing.

    See the corresponding documentation pages for more information and examples. 

## Iterate

The first time you run the tests, you will likely get many failures, but don't be discouraged. Often times the first few fixes you make will resolve many of the failures.

After you have made a potential fix, re-run the tests to ensure it solved the problem and then move on to the next failure. 
You should quickly pick up the general code patterns, and most of the effort is finding the correct SQL syntax for your database.

!!! tip
    
    If your database does not support a particular feature, make sure you are handling that in `validate()` or `supports()` methods as appropriate.   

## Complete

Once all the Test Harness tests are passing, you will be able to use any of the standard change types in XML/YAML/JSON changelogs as well as any snapshot-based functionality.
For example, all of this should now work:

- `liquibase update` with `<createTable...` in the changelog
- `liquibase update` with `<tableExists...` preconditions in the changelog
- `liquibase snapshot`
- `liquibase diff`
- `liquibase diff-changelog`
- `liquibase generate-changelog`

Now is a great time to release a new version of your extension :material-party-popper:

## Next Steps

If you are looking to move beyond standard functionality into custom change types or more advanced features, see the [Extension Guides](../index.md) 
for more information, including:

- [Add a Change Type](../add-a-change-type.md)
- [Add a Precondition](../add-a-precondition.md)
- [Add a Snapshot Generator](../add-a-snapshot-generator.md)
- [Add a Native Database Executor](../add-a-native-executor.md)