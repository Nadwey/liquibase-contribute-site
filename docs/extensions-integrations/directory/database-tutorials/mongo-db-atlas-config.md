---
title: MongoDB Atlas
---

<h1>MongoDB Atlas</h1>
<p>This guide covers two ways to authenticate a MongoDB Atlas instance with Liquibase. One uses the SCRAM (username/password) method and the other uses the X.509 Certificate method. Follow the one that fits your scenario best.</p>
<h2>Prerequisites</h2>
<p>Learn more about each step at the links below.</p>
<ol>
    <li value="1"><a href="https://www.mongodb.com/docs/atlas/tutorial/create-atlas-account/">Create a MongDB Atlas Account</a>
    </li>
    <li value="2"><a href="https://www.mongodb.com/docs/atlas/tutorial/deploy-free-tier-cluster/">Deploy a Free Cluster</a>
    </li>
    <li value="3"><a href="https://www.mongodb.com/docs/atlas/security/add-ip-address-to-list/">Add Your Connection IP Address to Your IP Access List</a>
    </li>
</ol>
<h2>SCRAM (Username/Password)</h2>
<p>Users often prefer the SCRAM method because of simplicity and increased security. The server stores passwords in a iterated hash format. This makes offline attacks harder, and decreases the impact of database breaches.</p>
<h3>MongoDB Atlas configuration</h3>
<ol>
    <li value="1"><a href="https://www.mongodb.com/docs/atlas/tutorial/create-mongodb-user-for-cluster/">Create a Database User for Your Cluster</a>
    </li>
    <li value="2">Add or change Database User role to <b>Atlas admin</b> (Security &gt; Database Access &gt; Edit &gt; Database User Privileges &gt; Built-in Role)</li>
    <p class="note" data-mc-autonum="&lt;b&gt;Note: &lt;/b&gt;"><span class="autonumber"><span><b>Note: </b></span></span>Learn more about database users and built in roles here: <a href="https://www.mongodb.com/docs/atlas/security-add-mongodb-users/#modify-database-users">Modify Database Users</a> and <a href="https://www.mongodb.com/docs/atlas/security-add-mongodb-users/#built-in-roles">Built In Roles</a>.</p>
</ol>
<h3>Liquibase configuration</h3>
<p>Once MongoDB&#160;Atlas is configured, you must then configure Liquibase.</p>
<ol>
    <li value="1">Add the <code>liquibase.command.url</code> property to the properties file, environment variables, or command line options in the following format:</li><pre><code class="language-text">liquibase.command.url: mongodb+srv://cluster0.abcd123.mongodb.net/lbcat</code></pre>
    <li value="2">Add the <code>liquibase.command.username</code> and <code>liquibase.command.password</code> properties to the same configuration file, environment variables, or command line. These are the same credentials entered in Step 1 above titled: <a href="https://www.mongodb.com/docs/atlas/tutorial/create-mongodb-user-for-cluster/">Create a Database User for Your Cluster</a></li>
</ol>
<p>MongoDB Atlas is now configured successfully with Liquibase.<br /></p>
<h2>X.509 Certificate</h2>
<p>This authorization mechanism, albeit more complex, allows system administrators to configure certificates for users within their organization. It also does not require you to memorize a password.</p>
<h3>MongoDB&#160;Atlas configuration</h3>
<ol>
    <li value="1"><a href="https://www.mongodb.com/docs/atlas/security-add-mongodb-users/#add-database-users">Add Database Users</a> for X.509 Certificates</li>
    <li value="2">Add or change Database User role to <b>Atlas admin</b> (Security &gt; Database Access &gt; Edit &gt; Database User Privileges &gt; Built-in Role)</li>
    <p class="note" data-mc-autonum="&lt;b&gt;Note: &lt;/b&gt;"><span class="autonumber"><span><b>Note: </b></span></span>Learn more about database users and built in roles here: <a href="https://www.mongodb.com/docs/atlas/security-add-mongodb-users/#modify-database-users">Modify Database Users</a> and <a href="https://www.mongodb.com/docs/atlas/security-add-mongodb-users/#built-in-roles">Built In Roles</a>.</p>
</ol>
<h3>Java configuration</h3>
<p>Java Truststore is a Java mechanism that stores Certificates. It is used only by Java applications. The below command creates the <code>CA.p12</code> Truststore file that contains the certificate which was pulled from MongoDB Atlas above in Step 4.</p>
<ol>
    <li value="1">Create the Truststore file by running the following in the CLI:</li><pre><code class="language-text">openssl pkcs12 -export -in X509-cert-137983036943191321.pem -name mongoAtlas -caname CA -out CA.p12 -passout pass:qwerty123</code></pre>
</ol>
<p>The <code>CA.p12</code> Truststore file that contains the certificate can now be used by Liquibase to connect to MongoDB Atlas.</p>
<h3>Liquibase configuration</h3>
<p>Once MongoDB Atlas and Java are configured, you must then configure Liquibase.</p>
<p class="note" data-mc-autonum="&lt;b&gt;Note: &lt;/b&gt;"><span class="autonumber"><span><b>Note: </b></span></span>Your connection to MongoDB&#160;Atlas must be TLS&#160;and SSL&#160;encrypted.</p>
<ol>
    <li value="1">Add the <code>liquibase.command.url</code> property to the properties file, environment variables, or command line options in the following format:</li><pre><code class="language-text">liquibase.command.url: mongodb+srv://cluster0.xtsabc123.mongodb.net/lbcat?authSource=%24external&amp;authMechanism=MONGODB-X509&amp;&amp;tlsCertificateKeyFile=X509-cert-137983036943191321.pem</code></pre>
    <li value="2">Add <code>JAVA_OPTS="-Djavax.net.ssl.keyStoreType=pkcs12 -Djavax.net.ssl.keyStore=CA.p12 -Djavax.net.ssl.keyStorePassword=qwerty123"</code> to the environment variables before running Liquibase commands.</li>
</ol>
<p>MongoDB Atlas is now configured successfully with Liquibase.<br /></p>
