---
title: To Liquibase 2.0
---
<h1>Liquibase 2.0 Extension Upgrade Guide</h1>
<p>Liquibase 2.0 introduces several non-compatible changes that will require action to upgrade from 1.9 to 2.0. For a list of new features in 2.0, see the <a href="https://www.liquibase.com/blog/liquibase-2-0-officially-released">2.0 features list</a>.</p>
<h2>Checksum Format Change</h2>
<p>Liquibase stores checksums for each change executed in the DATABASECHANGELOG table. These checksums are used to alert the user to changesets that have been changed after they were executed, and to handle <code>runOnChange="true"</code> changesets.</p>
<p>The way we compute these checksums changed in 2.0. The first time you update an existing database, Liquibase will detect the old format and upgrade the checksum values. During this first run, Liquibase will not be able to detect modified changesets or <code>runOnChange</code> requirements. If you are concerned about this, you may want to run a known unchanged changelog against the database with 2.0 before updating your new changelog.</p>
<h2>XSD Definition Change</h2>
<p>The format of the XSD definition has changed. The new format looks like:</p><pre xml:space="preserve"><code class="language-xml">&lt;databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog 
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-2.0.xsd"&gt;</code></pre>
<h2><code>ModifyColumn</code> tag Deprecated</h2>
<p>The <code>modifyColumn</code> tag has been deprecated and moved to the extension portal. If you are using <code>modifyColumn</code>, consider the new <code>&lt;modifyDataType&gt;</code> or other more specific commands (<code>addPrimaryKeyConstraint</code>, etc). You can continue to use the <code>modifyColumn</code> tag if you include <code>modify-column-2.0.0.jar</code> in your classpath. See <a href="https://github.com/liquibase/liquibase-modify-column"><code>modifyColumn</code> library</a> to get this jar.</p>
<h2>Columns added to table DATABASECHANGELOG</h2>
<p>Liquibase 2.0 will silently add three columns to your DATABASECHANGELOG table: <code>tag</code>, <code>OrderExecuted</code>, and <code>ExecType</code>. Older versions of Liquibase will be incompatible with this table because they will not supply values for these columns, two of which are not nullable.</p>
<p>SQL to manually make these changes is (may vary based on database):</p><pre><code class="language-sql">ALTER TABLE DATABASECHANGELOG ADD TAG VARCHAR(255);
ALTER TABLE DATABASECHANGELOG ADD ORDEREXECUTED INT;
UPDATE DATABASECHANGELOG SET ORDEREXECUTED = -1;
ALTER TABLE DATABASECHANGELOG MODIFY ORDEREXECUTED INT NOT NULL;
ALTER TABLE DATABASECHANGELOG ADD EXECTYPE VARCHAR(10);
UPDATE DATABASECHANGELOG SET EXECTYPE = 'EXECUTED';
ALTER TABLE DATABASECHANGELOG MODIFY EXECTYPE VARCHAR(10) NOT NULL;</code></pre>
<p>You should only need to use the above SQL for <code>update-sql</code> calls.</p>
<h2>Hibernate Integration Extracted</h2>
<p>The Hibernate integration has been moved to be a plugin rather than in the Liquibase core itself. If you use the Liquibase hibernate support, you'll need to add the JAR from the <a href="https://github.com/liquibase/liquibase-hibernate">hibernate extension</a> to your classpath.</p>
<h2>Diff parameter naming</h2>
<p>To remove confusion, the <code>baseUrl</code>, <code>baseUsername</code>, etc parameters used in performing database diffs have changed to <code>referenceUrl</code>, <code>referenceUsername</code>, etc.</p>
<h2>Maven Plugin</h2>
<p>The artifact name of the Maven plugin changed to <code>org.liquibase : liquibase-maven-plugin</code> from <code>org.liquibase : liquibase-plugin</code></p>
<h2>Servlet Listener</h2>
<p>The class name of <code>LiquibaseServletListener</code> changed to <code>liquibase.integration.servlet.LiquibaseServletListener</code>.</p>
<p>The context parameter names changed:</p>
<ul>
    <li><code>LIQUIBASE_DATA_SOURCE</code> -&gt; <code>liquibase.datasource</code></li>
    <li><code>LIQUIBASE_CHANGELOG</code> -&gt; <code>liquibase.changelog</code></li>
    <li><code>LIQUIBASE_CONTEXTS</code> -&gt; <code>liquibase.contexts</code></li>
    <li><code>LIQUIBASE_DEFAULT_SCHEMA</code> -&gt; <code>liquibase.schema.default</code></li>
    <li><code>LIQUIBASE_HOST_INCLUDES</code> -&gt; <code>liquibase.host.includes</code></li>
    <li><code>LIQUIBASE_HOST_EXCLUDES</code> -&gt; <code>liquibase.host.excludes</code></li>
    <li><code>LIQUIBASE_FAIL_ON_ERROR</code> -&gt; <code>liquibase.onerror.fail</code></li>
</ul>
<h2>Spring Integration</h2>
<p>The class name of the <code>SpringLiquibase</code> class has changed to <code>liquibase.integration.spring.SpringLiquibase</code></p>
<h2>Other package and class naming</h2>
<p>Many other classes changed their packages and/or names significantly. If you have more complex Liquibase integration and are not sure how to convert your code, post a question on the <a href="https://forum.liquibase.org/">Liquibase forum</a>.</p>
<h2><code>MANIFEST.MF</code> requirements for embedding</h2>
<p>Liquibase expects a <code>liquibase-package</code> property in a <code>MANIFEST.MF</code> file. If you are using the standard <code>liquibase.jar</code> you don't have to worry about it. But if you are embedding Liquibase to the point of not including the standard <code>MANIFEST.MF</code>, make sure you add the following to your <code>MANIFEST.MF</code>.</p><pre><code class="language-text">Liquibase-Package: liquibase.change,
liquibase.database,
liquibase.parser,
liquibase.precondition,
liquibase.serializer,
liquibase.sqlgenerator,
liquibase.executor,
liquibase.snapshot,
liquibase.logging,
liquibase.ext</code></pre>
