---
title: To Liquibase 3.1
---
<h1>Liquibase 3.1 Extension Upgrade Guide</h1>
<p>For must Liquibase end users, Liquibase 3.1 is a drop-in replacement for any Liquibase 3.0 version.</p>
<p>If you are using with relative paths or running against InterSystems Cache, SAP MaxDB or IBM DB2 for iSeries see the notes below.</p>
<p>For developers of Liquibase extensions, there has been some Java API changes that may impact your code.</p>
<p><a href="../lb-3.0-upgrade-guide">2.x to 3.0 upgrade guide</a>
</p>
<h2>Change to how referenced files are tracked</h2>
<p>With certain builds in 3.0.x, using with <code>relativeToChangeLog</code> caused the file path stored in DATABASECHANGELOG to have the full physical path, rather than classpath relative paths.</p>
<p>With 3.1.0, this bug is fixed, but if you have changelogs that have already ran, Liquibase may attempt to re-execute them because the filepath column is part of the changeset identifier and it now sees it as different.</p>
<p>You can resolve the problem in one of two ways:</p>
<ul>
    <li>Set a <a href="https://docs.liquibase.com/concepts/changelogs/attributes/logicalfilepath.html">logicalFilePath</a> in the included changelogs equal to the full path as it was stored before</li>
    <li>Manually update your DATABASECHANGELOG table to strip off the extra portion of the path. The SQL will vary by database, but an example for mysql would be:</li>
</ul><pre xml:space="preserve"><code class="language-text">update DATABASECHANGELOG set FILENAME=REPLACE(filename, 'c:\my\root\path', '')</code></pre>
<h2>Less common database support moved to extensions</h2>
<p>If you are using Liquibase with Cache, MaxDB or DB2 for iSeries, support has been moved out of Liquibase core and into extensions.</p>
<p>To re-enable support for these databases, install the corresponding extension:</p>
<ul>
    <li><a href="https://github.com/liquibase/liquibase-cache">InterSystems Cache</a>
    </li>
    <li><a href="https://github.com/liquibase/liquibase-maxdb">SAP MaxDB</a>
    </li>
    <li><a href="https://github.com/liquibase/liquibase-db2i">IBM DB2 for iSeries</a>
    </li>
</ul>
<h2>Class <code>liquibase.executor.Executor</code> changes</h2>
<p>The <code>liquibase.executor.Executor queryForList</code> methods now return <code>List&lt;Map&lt;String, ?&gt;&gt;</code> rather than just <code>List&lt;Map&gt;</code>.</p>
<h2>Interface <code>liquibase.database.Database</code> changes</h2>
<p>There is a new <code>addReservedWords(words)</code> method to implement. If extending <code>AbstractJdbcDatabase</code>, the default implementation should work for you.</p>
<p>The following methods were removed from <code>liquibase.database.Database</code> in favor of extensible service implementations:</p>
<table>
<tr><th>3.0</th><th>3.1</th></tr>
<tr>
    <td>hasDatabaseChangeLogLockTable()</td>
    <td>((StandardLockService)liquibase.lockservice.LockServiceFactory.getInstance().getLockService(database)).hasDatabaseChangeLogLockTable()</td>
</tr>
<tr>
    <td>checkDatabaseChangeLogLockTable()</td>
    <td>liquibase.lockservice.LockServiceFactory.getInstance().getLockService(database).init()</td> 
</tr>
<tr>
    <td>checkDatabaseChangeLogTable(updateExistingNullChecksums, databaseChangeLog, contexts)</td>
    <td>liquibase.changelog.ChangeLogServiceFactory.getInstance().getChangeLogService(database).init()</td>
</tr>
<tr>
    <td>hasDatabaseChangeLogTable()</td>
    <td>((StandardChangeLogHistoryService) liquibase.changelog.ChangeLogServiceFactory.getInstance().getChangeLogService(database)).hasDatabaseChangeLogTable()</td>
</tr>
<tr>
    <td>getNextChangeSetSequenceValue()</td>
    <td>liquibase.changelog.ChangeLogServiceFactory.getInstance().getChangeLogService(database).getNextSequenceValue()</td>
</tr>
</table>

<h2>Interface <code>liquibase.lockservice.LockService</code> changes</h2>
<p>New methods were added to the <code>LockService</code> interface:</p>
<ul>
    <li><code>init()</code>
    </li>
    <li><code>destroy()</code>
    </li>
</ul>
<p>The class <code>liquibase.lockservice.LockServiceImpl</code> has been renamed to <code>liquibase.lockservice.StandardLockService</code>, although a deprecated placeholder with the old name was introduced for backwards compatibility.</p>
<h2>Interface <code>liquibase.changelog.visitor.ChangeExecListener</code> changes</h2>
<p>New methods were added to the <code>ChangeExecListener</code> interface:</p>
<ul>
    <li>preconditionFailed</li>
    <li>preconditionErrored</li>
    <li>willRun</li>
    <li>ran</li>
</ul>
<h2>Interface <code>liquibase.snapshot.SnapshotGenerator</code> changes</h2>
<p>A new <code>replaces()</code> method was added to <code>SnapshotGenerator</code>. The default implementation in <code>JdbcSnapshotGenerator</code> is a no-op which should work for most uses.</p>
<h2>Standardized <code>ResourceAccessor</code> handling of missing files</h2>
<p><code>CommandLineResourceAccessor</code> now returns null if a file does not exist rather than throwing an IOException. This behavior now matches the other <code>ResourceAccessors</code>.</p>
<p>An IOException should only be thrown by a <code>ResourceAccessor</code> if the file exists but there is a problem reading it.</p>
