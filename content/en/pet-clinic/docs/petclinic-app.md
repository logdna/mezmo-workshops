---
title: Spring PetClinic Application
weight: 3
description: >
    Introduce the PetClinic application which is a Java application that will be used as the test bed for this workshop.
tags:
- java
- log4j
- structured-logging
- PetClinic
---

For this exercise, we will use the Spring PetClinic application. This is a very popular sample java application built with Spring framework (Spring Boot).

## Get Started

1. To get started, clone the PetClinic repository starting from our home directory:

   ```shell
   cd $HOME 
   git clone https://github.com/spring-projects/spring-petclinic
   ```


2. Change into the `spring-petclinic` directory:

   ```shell
   cd spring-petclinic
   ```

3. Start a MySQL database for Pet Clinic to use:

   ```shell
   docker run -d -e MYSQL_USER=petclinic -e MYSQL_PASSWORD=petclinic -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=petclinic -p 3306:3306 docker.io/mysql:5.7.8
   ```

4. Next, run the maven command to compile/build/package Pet Clinic:

   ```shell
   ./mvnw package -Dmaven.test.skip=true
   ```

   {{% alert title="Note" color="secondary" %}}
   This will take a few minutes the first time you run, maven will download a lot of dependencies before it actually compiles the app. Future executions will be a lot shorter.
   {{% /alert %}}

5. Once the compilation is complete, you can run the application with the following command:

   ```shell
   java -jar target/spring-petclinic-*.jar --spring.profiles.active=mysql
   ```

6. You can verify that the application is running by visiting `http://localhost:8080`. Click around, generate errors, add visits, etc.

## Enable Structured Logging

The PetClinic app is built using standard **Log4j** formatted log entries.  These log entries, while easily human readable, are much more difficult to machine parse due to inconsistencies in the log format.  It's easy enough to switch from human-readable logs to structured logs in JSON format.

1. Edit `pom.xml`, and insert the following list of dependencies at the end of the `<dependencies>...</dependencies>` section (around line 107):

    ```xml {linenos=table, linenostart=107}
   
       <!-- logback -->
       <dependency>
         <groupId>ch.qos.logback.contrib</groupId>
         <artifactId>logback-json-classic</artifactId>
         <version>0.1.5</version>
       </dependency>
   
       <dependency>
         <groupId>ch.qos.logback.contrib</groupId>
         <artifactId>logback-jackson</artifactId>
         <version>0.1.5</version>
       </dependency>
   
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-databind</artifactId>
         <version>2.14.0-rc1</version>
       </dependency>
   
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-core</artifactId>
         <version>2.13.4</version>
       </dependency>
       <!-- end of logback -->
   ```

   A `git diff` will look like this:
   
   ```shell
   git diff
   ```

    ```diff
    diff --git a/pom.xml b/pom.xml
    index c67bce2..3888d74 100644
    --- a/pom.xml
    +++ b/pom.xml
    @@ -105,6 +105,32 @@
         </dependency>
         <!-- end of webjars -->
    
    +    <!-- logback -->
    +    <dependency>
    +      <groupId>ch.qos.logback.contrib</groupId>
    +      <artifactId>logback-json-classic</artifactId>
    +      <version>0.1.5</version>
    +    </dependency>
    +
    +    <dependency>
    +      <groupId>ch.qos.logback.contrib</groupId>
    +      <artifactId>logback-jackson</artifactId>
    +      <version>0.1.5</version>
    +    </dependency>
    +
    +    <dependency>
    +      <groupId>com.fasterxml.jackson.core</groupId>
    +      <artifactId>jackson-databind</artifactId>
    +      <version>2.14.0-rc1</version>
    +    </dependency>
    +
    +    <dependency>
    +      <groupId>com.fasterxml.jackson.core</groupId>
    +      <artifactId>jackson-core</artifactId>
    +      <version>2.13.4</version>
    +    </dependency>
    +    <!-- end of logback -->
    +
         <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
    ```

2. The structured logging uses the **Logback** framework, so let's configure it to output as we need.  Create a new file `src/main/resources/logback.xml` and set the contents as:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration scan="true">
        <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
            <encoder>
                <pattern>%date{MMM dd HH:mm:ss} %-5level petclinic[1]: %msg %n</pattern>
            </encoder>
        </appender>
    
        <appender name="FileLogger" class="ch.qos.logback.core.FileAppender">
            <file>/tmp/petclinic.json</file>
            <append>false</append>
            <immediateFlush>true</immediateFlush>
    
            <layout class="ch.qos.logback.contrib.json.classic.JsonLayout">
                <jsonFormatter class="ch.qos.logback.contrib.jackson.JacksonJsonFormatter">
                    <prettyPrint>false</prettyPrint>
                </jsonFormatter>
                <timestampFormat>yyyy-MM-dd' 'HH:mm:ss</timestampFormat>
                <appendLineSeparator>true</appendLineSeparator>
            </layout>
        </appender>
    
        <root level="DEBUG">
            <appender-ref ref="FileLogger"/>
            <appender-ref ref="STDOUT"/>
        </root>
    </configuration>
    ```

3. We can now repackage the PetClinic application with:

   ```bash
   ./mvnw package -Dmaven.test.skip=true
   ```

4. Rerun the application with the following command:

   ```bash
   java -jar target/spring-petclinic-*.jar --spring.profiles.active=mysql
   ```
   
   After initialization, the remaining output should be JSON format:

   {{< highlight text >}}
              |\      _,,,--,,_
             /,`.-'`'   ._  \-;;,_
  _______ __|,4-  ) )_   .;.(__`'-'__     ___ __    _ ___ _______
 |       | '---''(_/._)-'(_\_)   |   |   |   |  |  | |   |       |
 |    _  |    ___|_     _|       |   |   |   |   |_| |   |       | __ _ _
 |   |_| |   |___  |   | |       |   |   |   |       |   |       | \ \ \ \
 |    ___|    ___| |   | |      _|   |___|   |  _    |   |      _|  \ \ \ \
 |   |   |   |___  |   | |     |_|       |   | | |   |   |     |_    ) ) ) )
 |___|   |_______| |___| |_______|_______|___|_|  |__|___|_______|  / / / /
 ==================================================================/_/_/_/

:: Built with Spring Boot :: 2.7.3

Oct 24 12:42:22 [background-preinit] DEBUG o.jboss.logging: Logging Provider: org.jboss.logging.Log4j2LoggerProvider
Oct 24 12:42:22 [main] INFO o.s.s.p.PetClinicApplication: Starting PetClinicApplication v2.7.3 using Java 14.0.2 on mbp1 with PID 88857 (/Users/bmeyer/spring-petclinic/target/spring-petclinic-2.7.3.jar started by bmeyer in /Users/bmeyer/spring-petclinic)
Oct 24 12:42:22 [background-preinit] INFO  o.h.v.i.u.Version: HV000001: Hibernate Validator 6.2.4.Final
Oct 24 12:42:22 [main] INFO  o.s.s.p.PetClinicApplication: The following 1 profile is active: "mysql"
Oct 24 12:42:22 [background-preinit] DEBUG o.h.v.i.x.c.ValidationXmlParser: Trying to load META-INF/validation.xml for XML based Validator configuration.
   {{< / highlight >}}

5. Our **Logback** configuration includes an entry to write the logs to the file `/tmp/petclinic.log`. Verify logs are being written to it as these will be used in the next section:

   ```shell
   more /tmp/petclinic.log
   ```
   
   {{< highlight shell >}}
{"timestamp":"2022-10-24 12:42:22","level":"DEBUG","thread":"background-preinit","logger":"org.jboss.logging","message":"Logging Provider: org.jboss.logging.Log4j2LoggerProvider","context":"default"}
{"timestamp":"2022-10-24 12:42:22","level":"INFO","thread":"main","logger":"org.springframework.samples.petclinic.PetClinicApplication","message":"Starting PetClinicApplication v2.7.3 using Java 14.0.2 on mbp1 with PID 88857 (/Users/bmeyer/spring-petclinic/target/spring-petclinic-2.7.3.jar started by bmeyer in /Users/bmeyer/spring-petclinic)","context":"default"}
{"timestamp":"2022-10-24 12:42:22","level":"INFO","thread":"background-preinit","logger":"org.hibernate.validator.internal.util.Version","message":"HV000001: Hibernate Validator 6.2.4.Final","context":"default"}
   ...
   {{< /highlight >}}

   {{% alert title="NOTE" color="info" %}}
   To ingest these logs properly, each log entry needs to appear on it's own line followed by an appended line separator.  We configured this in our **Logback** configuration with this line in the `FileLogger`'s `layout` config:

   ```xml
   			<appendLineSeparator>true</appendLineSeparator>
   ```
   {{% /alert %}}

