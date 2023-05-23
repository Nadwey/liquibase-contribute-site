---
title: With Maven
---

<h1 id="using-liquibase-with-spring-boot-and-maven-tutorial">Using Liquibase with Spring Boot and Maven Project</h1>
<p>Tip: To use the <code>liquibase-maven-plugin</code> and Spring Boot, see <a href="https://docs.liquibase.com/maven/workflows/using-liquibase-maven-plugin-and-springboot.htm">Using Liquibase Maven Plugin with Spring Boot</a>.</p>
<p>Use Liquibase with <a href="https://spring.io/projects/spring-boot#overview">Spring Boot</a> to create and configure standalone Spring applications and automate your database updates. Spring Boot with build systems like  Maven allows you to create Java applications started by running <code>java -jar</code> or <code>war</code> deployments.</p>
<p>The Liquibase Spring Boot integration ensures the application database is updated along with the application code by using Spring Boot auto-configuration and features.</p>
<p>To use Liquibase and Spring Boot:</p>
<ol>
    <li><a href="https://maven.apache.org/download.cgi">Install Maven</a> and <a href="https://maven.apache.org/install.html">add it to your path</a>.
    </li>
    <li>Ensure you have <a href="https://www.oracle.com/java/technologies/java-se-glance.html">Java Development Kit</a> (JDK 8, 11, or 16).
    </li>
    <li>Create a project by using the Spring Boot application:
    </li>
</ol>
<ul>
    <li>If you have an existing Spring Boot project, add the <code>liquibase-core</code> dependency to your project <code>pom.xml</code>.
    </li>
    <li>To manually create a new Spring Boot project, follow the <a href="https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/getting-started.html#getting-started.installing">Spring Boot Getting Started</a> documentation.
    </li>
    <li>To create a basic Spring Boot project, use a web-based service called <a href="https://start.spring.io/">Spring Initializr</a>.
    </li>
    <p>Enter the following information in Spring Initializr:</p>
</ul>
<ul>
    <ul>
        <li>Project: Maven</li>
        <li>Language: Java
    </li>
        <li>Spring Boot: the version you need
    </li>
        <li>Project Metadata:
    <ul><li>Group: com.example.liquibase
    </li><li>Artifact: springbootProject
    </li><li>Name: springbootProject
    </li><li>Description: Liquibase Project for Spring Boot
    </li><li>Package name: com.example.liquibase.springbootProject
    </li><li>Packaging: Jar
    </li><li>Java: 8, 11, or 16
    </li></ul></li>
        <li>Dependencies: Spring Data JPA and Liquibase Migration. The service lets you add your database driver dependency and any developer tool.
    </li>
    </ul>
</ul>
<ol start="4">
    <li>Select <b>GENERATE</b> to download your project template as a <code>.zip</code> file.
    </li>
    <li>Extract the files and open <code>pom.xml</code> in your IDE or text editor. By default, the Liquibase dependency will find the latest <code>liquibase-core</code> version. You can edit the Liquibase dependency to include the exact version of Liquibase you want to use.
    </li>
    <li>Follow the instructions depending on your project.
    </li>
</ol>
<h2>Running Liquibase with Spring Boot and the Maven project</h2>
<ol>
    <li>
        <p>Open the existing Spring Boot <code>application.properties</code> file. To find the file, navigate to <code>src/main/resources/application.properties</code>.</p>
    </li>
    <li>
        <p>Add the following properties to run Liquibase migrations. Update the values depending on your database requirements:</p>
    </li><pre xml:space="preserve"><code class="language-text">spring.datasource.url=jdbc:postgresql://localhost:5432/yourdatabase
spring.datasource.username=example
spring.datasource.password=example</code></pre>
    <p>Note: To find the URL format for your database, see <a href="https://docs.liquibase.com/start/tutorials/home.html">Liquibase Database Tutorials</a>.</p>
    <li>Create a new text file called <code>pom.xml</code> or use the <code>pom.xml</code> file created for your project by the Spring Initializr. </li>
    <li>Specify attributes in your <code>pom.xml</code> based on the example:</li><pre xml:space="preserve"><code class="language-xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
https://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
&lt;parent&gt;
         &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
         &lt;artifactId&gt;spring-boot-starter-parent&lt;/artifactId&gt;
         &lt;version&gt;2.5.3&lt;/version&gt;
         &lt;relativePath/&gt; &lt;!-- lookup parent from repository --&gt;
&lt;/parent&gt;
&lt;groupId&gt;com.example.liquibase&lt;/groupId&gt;
&lt;artifactId&gt;springbootProject&lt;/artifactId&gt;
&lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;
&lt;name&gt;springbootProject&lt;/name&gt;
&lt;description&gt;Liquibase Project for Spring Boot&lt;/description&gt;
&lt;properties&gt;
        &lt;java.version&gt;1.8&lt;/java.version&gt;
&lt;/properties&gt;
&lt;dependencies&gt;
       &lt;dependency&gt;
                 &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
                 &lt;artifactId&gt;spring-boot-starter-data-jpa&lt;/artifactId&gt;
       &lt;/dependency&gt;
       &lt;dependency&gt;
                 &lt;groupId&gt;org.liquibase&lt;/groupId&gt; 
                 &lt;artifactId&gt;liquibase-core&lt;/artifactId&gt;
       &lt;/dependency&gt;
       &lt;dependency&gt;
                 &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
                 &lt;artifactId&gt;spring-boot-starter-test&lt;/artifactId&gt;
                 &lt;scope&gt;test&lt;/scope&gt;
       &lt;/dependency&gt;

&lt;!-- https://mvnrepository.com/artifact/org.postgresql/postgresql --&gt;
       &lt;dependency&gt;
                 &lt;groupId&gt;org.postgresql&lt;/groupId&gt;
                 &lt;artifactId&gt;postgresql&lt;/artifactId&gt;
                 &lt;version&gt;42.2.5&lt;/version&gt;
       &lt;/dependency&gt;
&lt;/dependencies&gt;
 
&lt;build&gt;
     &lt;plugins&gt; 
             &lt;plugin&gt;
                  &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
                  &lt;artifactId&gt;spring-boot-maven-plugin&lt;/artifactId&gt;
             &lt;/plugin&gt;
     &lt;/plugins&gt;
&lt;/build&gt;

&lt;/project&gt;</code></pre>
            <li>Create a text file called <code>changelog.sql</code> or use the changelog file from the <code>examples</code> directory. Liquibase also supports the <code>.xml</code>, <code>.yaml</code>, or <code>.json</code> changelog formats.
            </li>
            <li>Specify the changelog file in the <code>application.properties</code> file as follows:
            </li><pre xml:space="preserve"><code class="language-xml">spring.liquibase.change-log=classpath:db/changelog/changelog.sql</code></pre>
            <p>Alternatively, specify the changelog file in <code>pom.xml</code> or the Liquibase properties file:</p>
            <p><code>pom.xml</code>: <code>&lt;changelog-file&gt;your/path/to/changelog.sql&lt;/changelog-file&gt;</code></p>
            <p><code>liquibase.properties</code>:</p>
            <ul>
                <li>Windows example: <code>changelog-file: ..\path\to\changelog.sql</code></li>
                <li>Linux example: <code>changelog-file: ../path/to/changelog.sql</code></li>
            </ul>
            <li>Add changesets to your changelog file. Use the following examples depending on the format of the changelog you created:</li>
            <p>XML example:</p><pre xml:space="preserve"><code class="language-xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:ext="http://www.liquibase.org/xml/ns/dbchangelog-ext"
    xmlns:pro="http://www.liquibase.org/xml/ns/pro"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-latest.xsd
        http://www.liquibase.org/xml/ns/dbchangelog-ext http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-ext.xsd
        http://www.liquibase.org/xml/ns/pro http://www.liquibase.org/xml/ns/pro/liquibase-pro-latest.xsd"&gt;
    &lt;changeSet id="1" author="Liquibase"&gt;
    &lt;createTable tableName="test_table"&gt;
           &lt;column name="test_id" type="int"&gt;
                 &lt;constraints primaryKey="true"/&gt;
           &lt;/column&gt;
           &lt;column name="test_column" type="varchar"/&gt;
    &lt;/createTable&gt;
    &lt;/changeSet&gt;
&lt;/databaseChangeLog&gt;</code></pre>
            <p>SQL example:</p><pre xml:space="preserve"><code class="language-sql">-- liquibase formatted sql

-- changeset liquibase:1
CREATE TABLE test_table (test_id INT, test_column VARCHAR, PRIMARY KEY (test_id))</code></pre>
            <p>YAML example:</p><pre xml:space="preserve"><code class="language-yaml">databaseChangeLog:
   - changeSet:
       id: 1
       author: Liquibase
       changes:
       - createTable:
           columns:
           - column:
               name: test_column
               type: INT
               constraints:  
                   primaryKey:  true  
                   nullable:  false  
                   tableName: test_table</code></pre>
            <p>JSON example</p><pre xml:space="preserve"><code class="language-json">{ 
  "databaseChangeLog": [
  {
	"changeSet": {
	  "id": "1",
      "author": "Liquibase",
	  "changes": [
	    {
		  "createTable": {
		    "columns": [
			{
			  "column": 
		      {
				"name": "test_column",
				"type": "INT",
				"constraints": 
			  {
				"primaryKey": true,
				"nullable": false
				}
				}
			  }]
			,
			"tableName": "test_table"
		  }
		}]
	  }
	}]
  }</code></pre>
            <li>Run your first migration with the following command:</li><pre><code class="language-text">mvn compile package</code></pre>
            <p>The command creates the Spring Boot application JAR file in the target directory. If the metadata specified in Spring Initializr was used in the Spring Boot project setup, you can directly execute the <code>jar</code> file by running:</p><pre><code class="language-text">java -jar springbootProject-0.0.1-SNAPSHOT.jar</code></pre>
        </ol>
<p>To roll back any changes, create the <code>liquibase.rollback-file</code> file in Spring Boot and generate a rollback script for changes associated with changesets:</p><pre><code class="language-text">-- rollback drop table test_table;</code></pre>
<p>Note: You can also use <a href="https://docs.liquibase.com/change-types/tag-database.html">tags</a> for the rollback.</p>
<h2 id="conclusion">Related links</h2>
<ul>
    <li><a href="https://docs.liquibase.com/tools-integrations/maven/home.html">Maven documentation</a>
    </li>
    <li><a href="https://spring.io/projects/spring-boot#learn">Spring Boot documentation</a>
    </li>
</ul>
