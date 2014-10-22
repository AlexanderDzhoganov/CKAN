# CKAN - New developer guidelines

## About this document

This document aims to explain the inner workings of CKAN as well as describing the currently agreed upon workflow. Hopefully, this will reduce friction for new developers entering CKAN development. Also, this document provides some level of insurance that significant knowledge of the codebase won't be lost if a developer leaves CKAN development.

As with anything CKAN, this document is always open to changes and improvements. Please note that any contribution must follow the [Contribution guidelines](https://github.com/KSP-CKAN/CKAN/blob/master/CONTRIBUTING.md) and the [Code of Conduct](https://github.com/KSP-CKAN/CKAN/wiki/Code-of-Conduct).

All code examples are in pseudo- C#. Due to CKAN being actively developed some details may have changed, but hopefully the program structure remains relatively the same (or we'd have to update this document).

## The CKAN project

In its essence, CKAN consists of a core library module ("Core"), a command-line client ("Command-line") and a GUI client ("the GUI") - collectively referred to as "CKAN" and a large number of supporting tools such as parsers, validators and bots ("CKAN Tools", "the tools" or "the toolset").

While CKAN itself is written exclusively in C# .NET, the toolset sports a variety of programming languages such as Perl, bash and others. Contributions to CKAN are expected to be in C#, use .NET 4.0 and be compatible with both Mono (Win32, Linux, MacOSX) and Visual Studio (Win32) environments. There is no such limitation present when contributing to the tools, they can be in any language and platform as long as it is [FOSS](http://en.wikipedia.org/wiki/Free_and_open-source_software) and is available on all of our development targets - modern Linux- based and Windows operating systems.

### Glossary

* ***CKAN client*** - an end-user software package that enables the user to manage his or her installed mods by using metadata provided by a CKAN repository

* ***CKAN repository*** - an online service which enables clients to fetch the latest available mod metadata

* ***CKAN module*** - a modification or enhancement to Kerbal Space Program, provided by mod authors to users. A module is a collection of files and the metadata related to those files.

* ***CKAN metadata*** - a JSON- encoded string that represents all the metadata related to a particular module without the contents of the module itself. It describes the module contents (e.g. version, author, download url, homepage etc.)
as well as the steps necessary to perform a complete installation. The metadata also contains information about the relationships between mods such as dependencies and recommendations.

* ***CKAN schema*** - the JSON schema (Draft v4) against which all metadata strings are validated before being accepted into a repository, the latest version of the schema is available [here](https://raw.githubusercontent.com/KSP-CKAN/CKAN/master/CKAN.schema)

* ***Mono*** - the FOSS [Mono project](http://www.mono-project.com/) is a complete .NET platform available for all modern desktop operating systems, used in lieu of Microsoft's .NET on platforms where it's not available (everything but Windows).

### CKAN Core

The core of CKAN contains all the code necessary to facilitate all basic operations such as communicating with a CKAN repository and installing available modules.

You can find all relevant code for this section in [master/CKAN/CKAN](https://github.com/KSP-CKAN/CKAN/tree/master/CKAN/CKAN).

[Here is a graph that contains all of the core classes and their relationships](http://i.imgur.com/o9jQxau.png). Don't be intimitated by the graph, CKAN's core is actually very clean and easily understandable, when you know the important stuff. So, here's the important stuff (in no particular order):

* CkanModule ([Module.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/Module.cs)) - a strongly- typed representation of CKAN metadata for a particular module. It contains accessors for all schema fields, for example:
```
CkanModule mod = ...;
Console.WriteLine(mod.download); 
```
will print the download URL for a mod

### CKAN Command-Line

### CKAN GUI

### CKAN Tools

## Setting up your development environment

### Linux- based operating systems

### Windows

## General workflow

### GitHub

#### Creating pull requests to master

#### Merging pull requests into master

#### Issues

### Waffle.io

## CKAN team communication

### GitHub issues

### IRC
