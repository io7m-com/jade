jade
===

[![Maven Central](https://img.shields.io/maven-central/v/com.io7m.jade/com.io7m.jade.svg?style=flat-square)](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.io7m.jade%22)
[![Maven Central (snapshot)](https://img.shields.io/maven-metadata/v?metadataUrl=https%3A%2F%2Fcentral.sonatype.com%2Frepository%2Fmaven-snapshots%2Fcom%2Fio7m%2Fjade%2Fcom.io7m.jade%2Fmaven-metadata.xml&style=flat-square)](https://central.sonatype.com/repository/maven-snapshots/com/io7m/jade/)
[![Codecov](https://img.shields.io/codecov/c/github/io7m-com/jade.svg?style=flat-square)](https://codecov.io/gh/io7m-com/jade)
![Java Version](https://img.shields.io/badge/17-java?label=java&color=e65cc3)

![com.io7m.jade](./src/site/resources/jade.jpg?raw=true)

| JVM | Platform | Status |
|-----|----------|--------|
| OpenJDK (Temurin) Current | Linux | [![Build (OpenJDK (Temurin) Current, Linux)](https://img.shields.io/github/actions/workflow/status/io7m-com/jade/main.linux.temurin.current.yml)](https://www.github.com/io7m-com/jade/actions?query=workflow%3Amain.linux.temurin.current)|
| OpenJDK (Temurin) LTS | Linux | [![Build (OpenJDK (Temurin) LTS, Linux)](https://img.shields.io/github/actions/workflow/status/io7m-com/jade/main.linux.temurin.lts.yml)](https://www.github.com/io7m-com/jade/actions?query=workflow%3Amain.linux.temurin.lts)|
| OpenJDK (Temurin) Current | Windows | [![Build (OpenJDK (Temurin) Current, Windows)](https://img.shields.io/github/actions/workflow/status/io7m-com/jade/main.windows.temurin.current.yml)](https://www.github.com/io7m-com/jade/actions?query=workflow%3Amain.windows.temurin.current)|
| OpenJDK (Temurin) LTS | Windows | [![Build (OpenJDK (Temurin) LTS, Windows)](https://img.shields.io/github/actions/workflow/status/io7m-com/jade/main.windows.temurin.lts.yml)](https://www.github.com/io7m-com/jade/actions?query=workflow%3Amain.windows.temurin.lts)|

## Repository Relocation

Development of this project has moved to an
[open-source but not open-contribution](https://sqlite.org/copyright.html#notopencontrib)
model.

Source code and commits will remain publicly available perpetually, but issues
and/or pull requests will be rejected and/or ignored. Additionally, this project
will now only be available via a read-only mirror at:

  https://codeberg.org/io7m-com/jade


## jade

The `jade` package provides an API for following good application directory
etiquette.

## Features

* API for handling application data directories.
* Implements the [XDG Basedir](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)
  specification on Unix-like platforms.
* Implements best-practice directory management on Windows platforms.
* Support for [portable applications](https://en.wikipedia.org/wiki/Portable_application).
* High-coverage automated test suite.
* Written in pure Java 17.
* [OSGi-ready](https://www.osgi.org/).
* [JPMS-ready](https://en.wikipedia.org/wiki/Java_Platform_Module_System).
* ISC license.

## Usage

### Modules

Applications use `jade` via the `com.io7m.jade.api` module, but should also
have the `com.io7m.jade.vanilla` module on the class/module path. If the
`com.io7m.jade.vanilla` module is not available, the `jade` package
will unconditionally return paths equivalent those returned by
[portable mode](#portable-mode).

### Using Directories

The `jade` package selects a set of application paths based on the current
operating system and the given configuration information. Getting access to the
basic set of application directories for an application named `Widget` is
straightforward:

```
final var configuration =
  ApplicationDirectoryConfiguration.builder()
    .setApplicationName("Widget")
    .build();

final var directories =
  ApplicationDirectories.get(configuration);

System.out.println("Configuration directory: " + directories.configurationDirectory());
System.out.println("Data directory: " + directories.dataDirectory());
System.out.println("Cache directory: " + directories.cacheDirectory());
```

### Portable Mode

Applications, particularly on Windows, often want to run in _portable mode_.
Portable mode essentially means that all of the paths used by the application
are given relative to the initial working directory of the application. This is
typically used to facilitate running applications from removable USB storage
devices without saving any data to the host operating system storage.

The `jade` API allows for specifying the name of a system property that will be
consulted in order to determine if the application wants to run in portable
mode. If the named system property has a value parsed as `true`, the API will
return paths relative to the application rather than system-specific paths.

```
final var configuration =
  ApplicationDirectoryConfiguration.builder()
    .setApplicationName("Widget")
    .setPortablePropertyName("com.io7m.example.portable")
    .build();

System.setProperty("com.io7m.example.portable", "true");

final var directories =
  ApplicationDirectories.get(configuration);

System.out.println("Configuration directory: " + directories.configurationDirectory());
System.out.println("Data directory: " + directories.dataDirectory());
System.out.println("Cache directory: " + directories.cacheDirectory());
```

### Override

The `jade` API allows for specifying the name of a system property that will be
consulted in order to determine if the application wants to override the
directory used to calculate application directories. If the named system
property exists, the value of the property will be treated as a path, and all
application directories will be calculated relative to this path.

```
final var configuration =
  ApplicationDirectoryConfiguration.builder()
    .setApplicationName("Widget")
    .setOverridePropertyName("com.io7m.example.override")
    .build();

System.setProperty("com.io7m.example.override", "/tmp/x");

final var directories =
  ApplicationDirectories.get(configuration);

// Prints: /tmp/x/config
System.out.println("Configuration directory: " + directories.configurationDirectory());

// Prints: /tmp/x/data
System.out.println("Data directory: " + directories.dataDirectory());

// Prints: /tmp/x/cache
System.out.println("Cache directory: " + directories.cacheDirectory());
```

