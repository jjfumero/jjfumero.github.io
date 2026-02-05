---
title: 'Building The Regression Test Harness for the OpenJDK (jtreg) from Source'
date: 2026-02-06
permalink: /posts/2026/02/05/jdk-jtreg-build
author_profile: false
tags:
 - JDK
 - jtreg
 - testing
 - Build
---

## Build JTREG

As in February 2026, to build `jtreg` from source, we need to use JDK 25. 

```bash
git clone https://github.com/openjdk/jtreg/
cd jtreg


## Use JDK 25 (e.g., Oracle JDK). We can use sdkman to 
## obtain the desired JDK version
sdk use java 25.0.1-oracle

## Build jtreg
sh ./make/build.sh

## Check
$ ./build/images/jtreg/bin/jtreg -version 

jtreg 8.3-dev+0
Installed in /home/juan/bin/jtreg/build/images/jtreg/lib/jtreg.jar
Running on platform version 25.0.1 from /home/juan/.sdkman/candidates/java/25.0.1-oracle.
Built with Java(TM) 2 SDK, Version 25.0.1+8-LTS-27 on February 04, 2026.
JT Harness, version 6.0 ea b24 (January 21, 2026)
Java Assembler Tools, version 9.1 ea 01 (January 21, 2026)
TestNG: testng-7.3.0.jar, guice-5.1.0.jar, jcommander-1.82.jar
JUnit: junit-platform-console-standalone-1.14.2.jar
```

To make `jtreg` easily accessible, it is convenient to declare the `JTREG_HOME` and update your `PATH` variable.

```bash
#JTREG_HOME
export JTREG_HOME=<path-to>/jtreg/build/images/jtreg
export PATH=<path-to>/jtreg/build/images/jtreg/bin:$PATH
```

## Build IDEA Plugin for JTREG

The `jtreg` repository also contains source code for an IntelliJ IDEA plugin.

```bash
cd ./plugins/idea
```

Update the file `gradle.properties` with the `jtregHome` path pointing to the `JTREG_HOME` we just built. 

```gradle
jtregHome = ../../build/images/jtreg
```

To build the plugin, we need JDK 21. 

```bash
sdk use java 21.0.9-oracle

sh gradlew clean build
```

The plugin is located at `plugins/idea/build/distributions/jtreg-plugin-1.19.zip`. 


To install it in IntelliJ IDEA:

1. Go to Settings > Plugins.
2. Click the Gear Icon ⚙️ and select "Install Plugin from Disk...".
3. Select the generated .zip file.
4. Restart your IDE.


