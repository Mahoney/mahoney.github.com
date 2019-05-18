---
layout: post
title: Maven Logging Config for Libraries & Applications
date: '2013-02-04T15:28:00.000Z'
author: Robert Elliot
tags:
modified_time: '2013-02-04T15:31:05.305Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-4122593573737722979
blogger_orig_url: http://blog.lidalia.org.uk/2013/02/maven-logging-config-for-libraries.html
---

A quick dump of my standard Maven poms for both libraries & applications.

Basic theory - pipe everything to SLF4J & use Logback as the SLF4J
implementation.

A library should ONLY have a dependency on slf4j-api - it should not use classes
in any logging implementation.

Libraries:
#### pom.xml
```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns="http://maven.apache.org/POM/4.0.0" xsi:schemalocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelversion>4.0.0</modelversion>
  <groupid>com.acme</groupid>
  <artifactid>my_library</artifactid>
  <version>1.2.3</version>

  <properties>
    <slf4jversion>1.7.1</slf4jversion>
  </properties>

  <repositories>
    <repository>
      <id>version99</id>
      <url>http://version99.qos.ch/</url>
    </repository>
  </repositories>

  <dependencies>
    <!-- COMPILE -->
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>slf4j-api</artifactid>
      <version>${slf4j.version}</version>
      <scope>compile</scope>
    </dependency>

    <!-- TEST -->
    <dependency>
      <groupid>ch.qos.logback</groupid>
      <artifactid>logback-classic</artifactid>
      <version>1.0.7</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>jcl-over-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>jul-to-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>uk.org.lidalia</groupid>
      <artifactid>jul-to-slf4j-config</artifactid>
      <version>1.0.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>log4j-over-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>commons-logging</groupid>
      <artifactid>commons-logging</artifactid>
      <version>99-empty</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupid>log4j</groupid>
      <artifactid>log4j</artifactid>
      <version>99-empty</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
#### src/test/resources/logback-test.xml
```xml
<configuration>
  <jmxconfigurator></jmxconfigurator>
  <contextlistener class="ch.qos.logback.classic.jul.LevelChangePropagator"></contextlistener>
  <root level="off"></root>
</configuration>
```
Application:
#### pom.xml
```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://maven.apache.org/POM/4.0.0" xsi:schemalocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelversion>4.0.0</modelversion>
  <groupid>com.acme</groupid>
  <artifactid>my_application</artifactid>
  <version>1.2.3</version>

  <properties>
    <slf4jversion>1.7.1</slf4jversion>
  </properties>

  <repositories>
    <repository>
      <id>version99</id>
      <url>http://version99.qos.ch/</url>
    </repository>
  </repositories>

  <dependencies>
    <!-- COMPILE -->
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>slf4j-api</artifactid>
      <version>${slf4jversion}</version>
      <scope>compile</scope>
    </dependency>

    <!-- RUNTIME -->
    <dependency>
      <groupid>ch.qos.logback</groupid>
      <artifactid>logback-classic</artifactid>
      <version>1.0.7</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>jcl-over-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>jul-to-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>uk.org.lidalia</groupid>
      <artifactid>jul-to-slf4j-config</artifactid>
      <version>1.0.0</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>org.slf4j</groupid>
      <artifactid>log4j-over-slf4j</artifactid>
      <version>${slf4jversion}</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>commons-logging</groupid>
      <artifactid>commons-logging</artifactid>
      <version>99-empty</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupid>log4j</groupid>
      <artifactid>log4j</artifactid>
      <version>99-empty</version>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```
#### src/main/resources/logback.xml
```xml
<configuration>
  <jmxconfigurator></jmxconfigurator>
  <contextlistener class="ch.qos.logback.classic.jul.LevelChangePropagator"></contextlistener>
  <appender class="ch.qos.logback.core.ConsoleAppender" name="sysout">
    <encoder>
       <pattern>%d [%thread] %-5level %logger{36} CLIENTID=%X{CLIENTID} SESSIONID=%X{SESSIONID} USERID=%X{USERID} TRANSACTIONID=%X{TRANSACTIONID} - %msg%n</pattern>
    </encoder>
  </appender>
  <root level="warn">
    <appender-ref ref="sysout">
  </appender-ref></root>
  <logger level="info" name="com.acme"></logger>
</configuration>
```
