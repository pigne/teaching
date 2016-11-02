---
layout: post
title: Java Web Apps Management with Maven
categories:
- JavaWebAppTools
- lecture
author: Yoann Pigné
---


## Install Maven and bootstrap a project

Install on Ubuntu 16.04

### Java
2 versiosn are availabe. The open source version and the Oracle version.

The Open Source:

```bash
sudo apt-get update
sudo apt-get install default-jdk
```

The Oracle version:

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

note : multiple Java versions can be handled with:

```bash
sudo update-alternatives --config java
```


### Apache Maven

```bash
sudo apt-get install maven
```


<p>Create a Web App with the <code>webapp</code> artifact.</p>

<pre><code class="bash">mvn archetype:generate  -DarchetypeArtifactId=maven-archetype-webapp
</code></pre>

3 parameters are asked. These are what identifies the project.

- `groupID`: usually the the revese DNS of the organisation (like Java package namming convention).
- `artifactId`: actual name of the project.
- `verison`: no precise versioning convention.
- `package`: java package path (usually equal to the `groupID`)

```XML
<groupId>org.pigne</groupId>
<artifactId>CountDownWebApp</artifactId>
<version>0.0.1</version>
```

Add a missing folder (for proper use with Eclipse later on)

```bash
mkdir CountDownWebApp/src/main/java
cd CountDownWebApp
```

We get development structure, different from a classical WAR file structure.

```
CountDownWebApp
├── pom.xml
└── src
    └── main
        ├── resources
        └── webapp
            ├── WEB-INF
            │   └── web.xml
            └── index.jsp
```



## Generated `pom.xml` file

```XML
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.pigne</groupId>
  <artifactId>CountDownWebApp</artifactId>
  <packaging>war</packaging>
  <version>0.0.1</version>
  <name>CountDownWebApp Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>CountDownWebApp</finalName>
  </build>
</project>
```

## Specify the Java version we want to compile the project

Add the <code>compiler</code> plugin to the list of plugins, in the <code>build</code> section of the <code>pom.xml</code> file.


```xml
<!-- ... -->
<build>
  <finalName>CountDownWebApp</finalName>
  <plugins>
      <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.0</version>
          <configuration>
              <source>1.7</source>
              <target>1.7</target>
          </configuration>
      </plugin>
    </plugins>
</build>
<!-- ... -->
```


<h2>Add some dependencies to be able to use Java EE libs</h2>

Dependencies are maintained thanks to repositories. The best known is **The Central Repositor** at <http://search.maven.org/>

We need to 2 dependencies:

- `servlet-api` an implementation of the  *servlet* standard

```xml
<dependency>
	<groupId>tomcat</groupId>
	<artifactId>servlet-api</artifactId>
	<version>5.5.23</version>
	<scope>provided</scope>
</dependency>
```

- `jsp-api`  an implementation of the  *JSP* stantard.

```xml
<dependency>
	 <groupId>tomcat</groupId>
	 <artifactId>jsp-api</artifactId>
	 <version>5.5.23</version>
	 <scope>provided</scope>
</dependency>
```


Note the <code>&lt;scope&gt;provided&lt;/scope&gt;</code> property the tells maven <b>not</b> to package those dependencies with the WAR file. Servlets and JSP are already part of the destination Application Server.



##  Tomcat
<p>An Lightweight Application Server and Web Server.</p>
<p>Not a complete Java EE platform.</p>




### Installation for Ubuntu 16.04


```bash
sudo apt-get install tomcat8 tomcat8-admin
```

### Configure authorizations

In file `/var/lib/tomcat8/conf/tomcat-users.xml`:

```xml

<tomcat-users>
	<role rolename="manager-gui" />
	<role rolename="manager-script" />
	<role rolename="admin-gui" />
	<user username="tomcat" password="123soleil"
	 roles="manager-gui,admin-gui,manager-script" />
</tomcat-users>
```



### Changing default port

By default Tomcat uses  port `8080`. It can be change in file ``/etc/tomcat8/server.xml` :

```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```


### Restart the server

```bash
sudo systemctl restart tomcat8
```



## Maven and Tomcat

Add and configure a <code>plugin</code> to the <code>build</code> configuration of the project in the <code>pom.xml</code> file.

```xml
<build>
<finalName>CountDownWebApp</finalName>
<pluginManagement>
  <plugins>
    <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
			<version>2.2</version>
      <configuration>
        <path>/${project.build.finalName}</path>
        <update>true</update>
        <url>http://localhost:8080/manager/text</url>
        <username>tomcat</username>
        <password>123soleil</password>
      </configuration>
    </plugin>
  </plugins>
</pluginManagement>
</build>
```

Note that the login and  password must reflect the ones int the Tomcat configuration.


## Compile and deploy to Tomcat with Maven


```bash
mvn tomcat7:redeploy
```



```bash
[INFO] tomcatManager status code:200, ReasonPhrase:OK
[INFO] OK - Application déployée pour le chemin de contexte /CountDownWebApp
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 9.014 s
[INFO] Finished at: 2016-11-01T18:38:22+01:00
[INFO] Final Memory: 20M/60M
[INFO] ------------------------------------------------------------------------

```


Let's enjoy our first Web App : <http://localhost:8080/CountDownWebApp/>


## Create a Servlet

Java code needs to be located in  <code>src/main/java/</code> folder.


Create subfolder that match the `package` (e.g. `your.package`) property specified in the `pom.xml`.

### The Servlet subclass

Create file  `src/main/java/*your*/*package*/CountDown.java`:

```java
package your.package;

import java.io.IOException;
import java.io.PrintWriter;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class CountDown extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
		resp.setContentType("text/html");
		resp.setCharacterEncoding("UTF-8");
		PrintWriter out = resp.getWriter();
		out.println("<!DOCTYPE html>");
		out.println("<html>");
		out.println("<head>");
		out.println("<meta charset=\"utf-8\" />");
		out.println("<title>CountDown</title>");
		out.println("</head>");
		out.println("<body>");
		out.println("<p>"+diff()+"</p>");
		out.println("</body>");
		out.println("</html>");

	}
	private String diff(){
		String theDate = "02/11/2016 17:30:00";
		String pattern = "dd/MM/yyyy HH:mm:ss";
		Date d2 = null;
		try {
			d2 = new SimpleDateFormat(pattern).parse(theDate);
		} catch (ParseException e) {
			return "server error...";
		}
		Date d1 = new Date();

		long diff = d2.getTime() - d1.getTime();

		long diffSeconds = diff / 1000 % 60;
		long diffMinutes = diff / (60 * 1000) % 60;
		long diffHours = diff / (60 * 60 * 1000) % 24;
		long diffDays = diff / (24 * 60 * 60 * 1000);
		return diffDays+" jour(s) "+diffHours+" heure(s) "+diffMinutes+" minute(s) "+diffSeconds+" seconde(s)";

	}
}
```

### Declare the Servlet and associate a route to it

The main file in the project is <code>web.xml</code> in <code>src/main/webapp/WEB-INF/</code>:

```xml
<web-app>
	<servlet>
		<servlet-name>CountDown</servlet-name>
		<servlet-class>your.package.CountDown</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>CountDown</servlet-name>
		<url-pattern>/countdown</url-pattern>
	</servlet-mapping>
</web-app>
```

- Compile : `mvn compile`
- Deploy : `mvn tomcat7:redeploy`
- Test : <http://localhost:8080/CountDownWebApp/countdown>




## Create A JSP

- We don't want to write HTML code in the java Servlet code : separation of concerns
- First we create a simple JSP in the public folder : <code>src/main/webapp/CountDownView.jsp</code>

```html
<%@ page pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
	<head>
	<meta charset="utf-8" />
	<title>CountDown</title>
	</head>

	<body>
		<p>Je suis une page générée dynamiquement avec JSP, mais je ne peux pas vous donner l'heure...</p>
	</body>
</html>
```


<p>Test this JSP's automaticaly generated Servlet : <a href="http://localhost:8080/CountDownWebApp/CountDownView.jsp">http://localhost:8080/CountDownWebApp/CountDownView.jsp</a></p>
<p>Check the Generated java code in the <code>work</code> folder of the  Tomcat App server (`/var/lib/tomcat8/work/**/CountDownWebApp/**/CountDownView_jsp.java`).</p>




## Have the Servlet use the JSP as a view

### Hide the JSP behind the <code>WEB-INF</code> folder.


```bash
mv src/main/webapp/CountDownView.jsp src/main/webapp/WEB-INF
```


### Modify the servlet


```java
@Override
public void doGet( HttpServletRequest request, HttpServletResponse response )
	throws ServletException, IOException {
	this.getServletContext()
		.getRequestDispatcher( "/WEB-INF/CountDownView.jsp" )
		.forward( request, response );
}
```


Compile / Deploy / Test : <http://localhost:8080/CountDownWebApp/countdown>


## Transfer data to the JSP

Modify the Servlet:

```java
@Override
public void doGet( HttpServletRequest request, HttpServletResponse response )
	throws ServletException, IOException {
	String diff = diff();
	request.setAttribute( "diff", diff );
	this.getServletContext().getRequestDispatcher( "/WEB-INF/CountDownView.jsp" ).forward( request, response );
}
```



## Transfer data to the JSP

Modify the JSP:

```html
<%@ page pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8" />
	<title>CountDown</title>
	<style>
	blockquote {
		font-style: italic;
		padding: 20px;
	}

	blockquote footer{
		color:#555;
		font-weight: bold
	}
	</style>
</head>

<body>
	<blockquote>
	&laquo; Il reste
	<%
            String diff = (String) request.getAttribute("diff");
            out.println( diff );
	%>
	avant la fin de ce cours ! &raquo;
	<footer>
	<%
            String parametre = request.getParameter( "author" );
            out.println( parametre );
	%>
	</footer>
	</blockquote>
</body>
</html>
```

Compile / Deploy / Test : <http://localhost:8080/CountDownWebApp/countdown>



## WebApps with Eclipse

A dedicated version of Eclipse for Java EE.

Integration of Maven and Application Servers (e.g. Tomcat).




## Install Eclipse for Java EE

- Download **Eclipse IDE for Java EE Developers** from the Eclipse download site : http://www.eclipse.org/downloads/.
- Eclipse is self-contained in the downloaded archive.

## Maven & Eclipse

- Import the project in Eclipse as an *existing Maven project* :
  `File > Import > Maven > Existing Maven Projects`

<p class='text_center'>
![Import Eclipse Project]({{ site.baseurl }}/images/importEclipse.png)
</p>




## Run the project in Eclipse

We create a *run configuration* to execute the maven target: `Run > Run configurations`

<p class='text_center'>
![Import Eclipse Project]({{ site.baseurl }}/images/mavenConfiguration.png)
</p>



## An application server in Eclipse

- First, switch off our Tomcat server:

```bash
systemctl stop tomcat8
```

- Then, configure a Tomcat server in Eclipse:
  `Window > Show View > Servers`

<p class='text_center'>
![Import Eclipse Project]({{ site.baseurl }}/images/Capture_new_App_server.png)
</p>



## Configure Tomcat and Eclipse on Ubuntu 16.04


- Give the install folder name of your Tomcat's install directory. Probably `/usr/share/tomcat8/`.
- You'll get an error. Click finish and ignore error.
- Copy Tomcat configuration to workspace :

```
sudo cp -r /etc/tomcat8/* ~/workspace/Servers/Tomcat\ v8.0\ Server\ at\ localhost-config/
sudo chown -R $USER:$USER ~/workspace/Servers/Tomcat\ v8.0\ Server\ at\ localhost-config/
```

- Concat policy files into one file:

```bash
cd ~/workspace/Servers/Tomcat\ v8.0\ Server\ at\ localhost-config/
cat policy.d/* > catalina.policy
```
- refresh the server configuration

## Run your project

Now you can run you project. Any modification to the code (upon saving the file) will be compiled and published to Tomcat!

<p class='text_center'>
![Import Eclipse Project]({{ site.baseurl }}/images/runOnServer.png)
</p>
