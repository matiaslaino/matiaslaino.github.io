---
title: Installing Open JDK 7 in Ubuntu (Tested in 17.04)
date: 2017-10-07T00:23:03+02:00
draft: false
toc: false
images:
tags:
  - ubuntu
  - openjdk
  - java
---

## Installing Open JDK 7 in Ubuntu (Tested in 17.04)

[This is the only approach I could find that worked](https://askubuntu.com/a/803616/742055).

Copy of the steps:

1. Download the packages intended for your architecture:
(for most users, this means amd64 if 64bit, or i386 if 32bit Ubuntu is installed)
  - [openjdk-7-jdk](https://packages.debian.org/experimental/openjdk-7-jdk)
  - [openjdk-7-jre](https://packages.debian.org/experimental/openjdk-7-jre)
  - [openjdk-7-jre-headless](https://packages.debian.org/experimental/openjdk-7-jre-headless)
  - [libjpeg62-turbo](https://packages.debian.org/sid/libjpeg62-turbo)
  - [libfontconfig1](https://packages.debian.org/sid/libfontconfig1)
  - [fontconfig-config](https://packages.debian.org/sid/fontconfig-config)
2. (Attempt to) install the packages using dpkg:
	```bash sudo dpkg -i openjdk-7-* libjpeg62-turbo* libfontconfig1* fontconfig-config* ```
  Check the output from dpkg. If there were dependency problems - which is likely - you will see the following (with your architecture substituted for amd64):
  > Errors were encountered while processing:
  openjdk-7-jre:amd64
  openjdk-7-jre-headless:amd64
  openjdk-7-jdk:amd64
  If there were no dependency issues, great, you're done, skip to #4. Otherwise, if you need to resolve some dependency issues, this is handled with:
3. ```bash sudo apt install -f```
  Notice, there is no need to re-run dpkg after letting apt resolve dependencies. It will automatically finish installation of the openjdk packages.
4. Update java alternatives. You can view all installed java versions with update-java-alternatives --list. To activate OpenJDK Java 1.7, run:
  ```bash sudo update-java-alternatives -s java-1.7.0-openjdk-amd64```
  You may notice an error about the IcedTeaPlugin.so plugin being unavailable. This isn't a real concern for developers working with the JDK.
5. Verify java is working:
  ```bash java -version```
  which should output something similar to:
  >java version "1.7.0_121"
    OpenJDK Runtime Environment (IcedTea 2.6.8) (7u121-2.6.8-1)
    OpenJDK 64-Bit Server VM (build 24.121-b00, mixed mode)