---
title: To Liquibase 4.0
---

<h1>Liquibase 4.0 Extension Upgrade Guide</h1>
<h2>Changes introduced in 4.0.0</h2>
<p>There are four major changes introduced in 4.0:</p>
<ol>
    <li>Changed <a href="#How_Extension_Classes_are_Configured_and_Found">how extension classes are configured and found</a></li>
    <li>Changed <a href="#How__files_are_found">how changelog files are found</a></li>
    <li>Changed <a href="#How_logging_works">how logging works</a></li>
    <li>Added <a href="#A_new_scope_object">a new Scope object</a></li>
</ol>
<p>We also made a collection of minor API changes that we will cover in more detail below.</p>
<h2><a name="How_Extension_Classes_are_Configured_and_Found"></a>How extension classes are configured and found</h2>
<p>Prior to 4.0, we had a custom ServiceLocator class which relied on custom logic to find all the classes that implemented our base interfaces. We'd specify a set of package names in a <code>Liquibase-Packages</code> section of <code>MANIFEST.MF</code> and Liquibase would go through all the configured classloaders to find classes that are in those packages.</p>
<p>Unfortunately, Java's classloader interface is not well set up for that type of logic. All of our attempts at workarounds often conflicted with logic in application-specific classloader implementations. It was a source of many issues.</p>
<p>Starting with 4.0, we switched to the standard <code>java.util.ServiceLoader</code> system to find extension classes. The Java ServiceLoader system works as follows:</p>
<ul>
    <li>Create a file in <code>META-INF/services</code> whose name matches the interface you are implementing</li>
    <li>In that file, add the list of all classes that implement that interface.</li>
</ul>
<p>For example, in Liquibase we have a file named <code>META-INF/services/liquibase.database.Database</code> that contains:</p><pre><code class="language-text">liquibase.database.core.DB2Database
liquibase.database.core.Db2zDatabase
liquibase.database.core.DerbyDatabase
liquibase.database.core.Firebird3Database
liquibase.database.core.FirebirdDatabase
liquibase.database.core.H2Database
liquibase.database.core.HsqlDatabase
liquibase.database.core.InformixDatabase
liquibase.database.core.Ingres9Database
liquibase.database.core.MSSQLDatabase
liquibase.database.core.MariaDBDatabase
liquibase.database.core.MockDatabase
liquibase.database.core.MySQLDatabase
liquibase.database.core.OracleDatabase
liquibase.database.core.PostgresDatabase
liquibase.database.core.SQLiteDatabase
liquibase.database.core.SybaseASADatabase
liquibase.database.core.SybaseDatabase
liquibase.database.core.UnsupportedDatabase</code></pre>
<p>The ServiceLoader will look for all files named <code>META-INF/services/liquibase.database.Database</code> in the classpath and use the union of all the classes listed in them as implementations.</p>
<h3>How this impacts your extension</h3>
<p>Because Liquibase no longer scans the <code>Liquibase-Packages</code> directories anymore, extensions need to be updated to list all their implementations in <code>META-INF/services</code>.</p>
<h3>To do</h3>
<p>For each class you created that implements a Liquibase extension point, add your class name to a file in <code>META-INF/service</code>s that matches the name of the Liquibase interface it implements.</p>
<p>For example, if you have created <code>com.example.liquibase.MyCreateTableChange</code> and <code>com.example.liquibase.MyAlterTableChange</code> classes that implement the <code>liquibase.change.Change</code> class, you must create the file <code>META-INF/services/liquibase.change.Change</code> containing the text:</p><pre><code class="language-html">com.example.liquibase.MyAlterTableChange
com.example.liquibase.MyCreateTableChange</code></pre>
<p><b>Note:</b> For extensions that have NOT been updated to list their classes this way, the <code>liquibase-compat</code> library includes the old classpath-scanning logic in it, so adding <code>liquibase-compat</code> to your classpath will allow extensions that have not made this change to continue to work for now.</p>
<p>The APIs around ServiceLocator have changed, so any extensions that have their own custom ServiceLocators or interact with the shipped ones will need to be updated. We have not introduced any backwards-compatibility into these classes because they are low-level and rarely-used directly in extensions. If you are using these classes and have questions on the changes, contact us by email, on <a href="https://discord.com/channels/700506481111597066/700506481572839505">Discord</a>, or on the <a href="https://forum.liquibase.org/">Liquibase Forum</a>.</p>
<h2><a name="How__files_are_found"></a>How changelog files are found</h2>
<p>Prior to 4.0, the way we find changelog files (both top-level files as well as included/referenced files) was mixed in with the logic on how to look up extension classes. Logic around how to handle things like directory-delimiter differences and encoding handling was also scattered and duplicated throughout the code.</p>
<p>Starting with 4.0, we've completely split the ResourceAccessor code from the ServiceLocator code, allowing both to do what they do best and not get in each other's way.</p>
<h3>How this impacts your extension</h3>
<p>The APIs around ResourceAccessor have changed, so any extensions that have their own custom ResourceAccessor or interact with the shipped ones will need to be updated. We have not introduced any backwards-compatibility into these classes because they are low-level and rarely-used directly in extensions.</p>
<p>Most likely, extensions are using the stream handling that exists in ResourceAccessor. These APIs have been cleaned up and it was too difficult to introduce old-api compatible versions alongside the new. Most extensions used the <code>StreamUtil.openStream()</code> method which has been deprecated but still exists.</p>
<h3>To do</h3>
<p>Search and replace all instances of the following methods:</p>
<table>
    <thead>
        <tr>
            <th>Old Method</th>
            <th>New Method</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>StreamUtil.openStream()</code>
            </td>
            <td><code>resourceAccessor.openStream()</code>
            </td>
        </tr>
        <tr>
            <td><code>new FileSystemResourceAccessor(String)</code>
            </td>
            <td><code>new FileSystemResourceAccessor(File)</code>
            </td>
        </tr>
    </tbody>
</table>
<p>If you have questions on making the changes, you can contact us by email, on <a href="https://discord.com/channels/700506481111597066/700506481572839505">Discord</a>, or on the <a href="https://forum.liquibase.org/">Liquibase Forum</a>.</p>
<h2><a name="How_logging_works"></a>How logging works</h2>
<p>Prior to 4.0, we used a custom logging interface around slf4j. The custom interface was there to allow alternate logging methods through extensions. The logging API was also used for both logs and user messages that should be sent to the console.</p>
<p>Starting with 4.0, we still have the custom logging API, but switched the default implementation to use <code>java.util.Logging</code>. The extra dependency on slf4j and indirection it provided was not giving us enough value for the cost. We also split out a new <code>liquibase.ui.UIService</code> for user messages that need to be routed to the “UI”.</p>
<h3>How this impacts your extension</h3>
<p>The logging API has shifted slightly, but we tried to keep it compatible for most use cases without introducing too much deprecated code.</p>
<p>As part of the shift to <code>java.util.Logging</code>, we better defined what the various levels are based on how <code>java.util.Logging</code> defines them. The main change is that they have a “fine” level, not a ”debug” level. Because log.debug(“message here“) is used so much, we kept that a deprecated method so that code continues to work.</p>
<p>We also used to have a version of the log methods that took an initial <code>liquibase.logging.Target</code>parameter to specify if the messages went to the log or to the UI. Because most extensions are simply specifying the default method without the parameter, we didn't bother to include a deprecated Target enum and methods.</p>
<h3>To do</h3>
<p>If you are using one of those logging methods, either remove the <code>liquibase.logging.Target</code> parameter to send the message to the log, or use the new UIService.</p>
<h2><a name="A_new_scope_object"></a>A new scope object</h2>
<p>One issue around preserving API compatibility is handling new parameters that need to be passed to methods. For example, if we realize a piece of code needs access to the Database object, we need to change a whole chain of method parameters to pass that Database object along from the point where we have it to the point where we need it. Method changes like that can be very API-breaking.</p>
<p>Starting with 4.0, we are introducing a new <code>liquibase.Scope</code> object. The job of the Scope object is to be a mid-point between global variables and method-level parameters. It allows us to set objects in one place in the code and access it from another point without to pass them along in method parameters.</p>
<p>You can access the scope with <code>Scope.getCurrentScope()</code> and add to the scope with <code>Scope.child()</code></p>
<p>We are still experimenting with best practices in what goes in the Scope and what does not, and are just starting to utilize it. But, the plan is to have it fully incorporated in to the API design by the end of the 4.x series.</p>
<p>For now, we are using the Scope object to better manage singleton instances, and have replaced methods that took ResourceAccessors, ClassLoaders, and ServiceLocators with new methods that do not take those as parameters and instead get them off the Scope.</p>
<h3>How this impacts your extension</h3>
<p>If you are accessing singleton objects like the Logger or DatabaseFactory, the correct way to access them is now <code>Scope.getCurrentScope().getSingleton(DatabaseFactory.class)</code> rather than <code>DatabaseFactory.getInstance()</code>. We do still have the <code>getInstance()</code> methods in the API, but they are marked as deprecated.</p>
<p>For commonly used singletons, we have helper methods directly on the Scope object. The Logger in particular should now be accessed by <code>Scope.getCurrentScope().getLog(getClass()</code> rather than <code>LogFactory.getLogger().getLog(getClass())</code>.</p>
<h3>To do</h3>
<p>Search and replace all instances of the following methods:</p>
<table>
    <thead>
        <tr>
            <th>Old Method</th>
            <th>New Method</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>DatabaseFactory.getInstance()</code>
            </td>
            <td><code>Scope.getCurrentScope().getSingleton(DatabaseFactory.class)</code>
            </td>
        </tr>
        <tr>
            <td><code>LogFactory.getLogger().getLog(getClass())</code>
            </td>
            <td><code>Scope.getCurrentScope().getLog(getClass())</code>
            </td>
        </tr>
    </tbody>
</table>
<h2>Misc API Cleanup</h2>
<p>Beyond those major changes in 4.0, we did some additional API cleanup work as well.</p>
<p><code>liquibase.util.StringUtils → liquibase.util.StringUtil</code>
</p>
<p>In 3.x, we had the plural <code>liquibase.util.StringUtils</code> which differed than the singular naming in all our other “Util” classes. We fixed that difference in core, and introduced a deprecated StringUtils in <code>liquibase-compat.jar</code>.</p>
<h3>To do</h3>
<p>Search and replace all instances of the following method:</p>
<table>
    <thead>
        <tr>
            <th>Old Method</th>
            <th>New Method</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>liquibase.util.StringUtils</code>
            </td>
            <td><code>liquibase.util.StringUtil</code>
            </td>
        </tr>
    </tbody>
</table>
<p><code><b>liquibase.sdk.database.MockDatabase → liquibase.database.core.MockDatabase</b></code>
</p>
<p>If you were using the MockDatabase class for testing, it has been moved to a different package because we cleared out the entire sdk package.</p>
<h3>Others</h3>
<p>There were other misc method signature changes as well, but they are all very low level and little used by extensions.</p>
<h2>Questions and Problems</h2>
<p>If you have questions on the API changes or issues with your extension supporting Liquibase 4.0, don't hesitate to contact us by email, on <a href="https://discord.com/login?redirect_to=%2Fchannels%2F700506481111597066%2F700506481572839505">Discord</a>, or on the <a href="https://forum.liquibase.org/">Liquibase Forum</a>.</p>
