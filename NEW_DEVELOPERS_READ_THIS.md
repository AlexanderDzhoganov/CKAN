# CKAN - New contributor guidelines

## About this document

This document aims to explain the inner workings of CKAN as well as describe the currently agreed upon workflow. Hopefully, this will reduce friction for new contributors entering CKAN development.

As with anything CKAN, this document is always open to corrections and improvements. Please note that any contribution must follow the [Contribution guidelines](https://github.com/KSP-CKAN/CKAN/blob/master/CONTRIBUTING.md) and the [Code of Conduct](https://github.com/KSP-CKAN/CKAN/wiki/Code-of-Conduct).

Due to CKAN being actively developed this document may be incomplet or inkorrekt, some details may change or may have already changed. Hopefully the program structure remains relatively the same (or it'd be reasonable to update this).

All code examples are in pseudo- C#. All typenames look like `SomeClassType`. This is some code:
```
void foo() { return 1; }

foo(1);
```

## The CKAN project

In its essence, CKAN consists of a core library module ("core"), a command-line client ("the command-line") and a GUI client ("the GUI") - collectively referred to as "CKAN" and a large number of supporting tools such as parsers, validators and bots ("CKAN Tools", "the tools" or "the toolset").

While CKAN itself is written exclusively in C# .NET, the toolset sports a variety of programming languages such as Perl, bash and others. Contributions to CKAN are expected to be in C#, use .NET 4.0 and be compatible with both Mono (Win32, Linux, MacOSX) and Visual Studio (Win32) environments. There is no such limitation present when contributing to the tools, they can be in any language and use any platform as long as it is [FOSS](http://en.wikipedia.org/wiki/Free_and_open-source_software) and is available on all of our development targets - modern Linux- based and Windows operating systems.

### Glossary

* ***Client*** - an end-user software package that enables the user to manage his or her installed mods by using metadata provided by a CKAN repository

* ***Repository*** - an online service which enables clients to fetch the latest available mod metadata and mod authors to upload new metadata

* ***Module*** - A modification or enhancement to Kerbal Space Program, provided by mod authors to users under a certain license. A module is a collection of files, their licensing and the metadata related to those files. Uniquely identified by a string called `identifier`

* ***Metadata*** - a JSON- encoded string that represents all the metadata related to a particular module without the contents of the module itself. It describes the mod itself (e.g. version, author, download url, homepage etc.)
as well as the steps necessary to perform a complete installation. The metadata also contains information about the relationships between mods such as dependencies and recommendations. Note that the metadata does not usually fall under the same license as the mod contents.

* ***Schema*** - the JSON schema ([Draft v4](http://json-schema.org/latest/json-schema-core.html)) against which all metadata strings are validated before being accepted into a repository, the latest version of the schema is available [here](https://raw.githubusercontent.com/KSP-CKAN/CKAN/master/CKAN.schema)

* ***Mono*** - the FOSS [Mono project](http://www.mono-project.com/) is a complete .NET- compatible platform available for all modern desktop operating systems. Used in lieu of Microsoft's .NET where the latter is not available (more or less everything but Windows).

### CKAN Core

The core of CKAN contains all the code necessary to facilitate basic package management operations such as communicating with a CKAN repository and installing modules.

You can find all relevant code for this section in [master/CKAN/CKAN](https://github.com/KSP-CKAN/CKAN/tree/master/CKAN/CKAN).

[Here is a graph that contains all of the core classes and their relationships](http://i.imgur.com/o9jQxau.png). Don't be intimidated by the amount of classes, CKAN's core is very understandable when you are aware of the important stuff. So, here's the important stuff (in no particular order):

#### Module ([Module.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/Module.cs))
* A base class for modules. 
* Strongly- typed representation of CKAN metadata.
* Modules are uniquely identified by their `string identifier` field, two different mods cannot have the same identifier (this is enforced at repository level)
* Contains accessors for all schema fields, for example:
```
CkanModule mod = ...;
Console.WriteLine(mod.download); 
```
will print the download URL for a mod. Note that optional fields in the schema can result in `null` values, so remember to check!

#### CkanModule ([Module.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/Module.cs))
* A concrete instance of a CKAN module.
* Inherits from `Module` and allows converting JSON to a CkanModule and vice versa.
* Available `CkanModule`s are generated at run-time by JSON- parsing repository- obtained .ckan metadata files and are located in the `Registry`.
* This class will probably get merged with Module as we [phase out bundles](https://github.com/KSP-CKAN/CKAN/issues/113)

#### Registry ([Registry.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/Registry.cs))
* Represents a database of modules
* The two important fields are two dictionaries - `available_modules` and `installed_modules`. Both are indexed by a module identifier. 
* `available_modules` is a dictionary mapping a `string` module identifier to an `AvailableModule` ([AvailableModule.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/AvailableModule.cs)) instance. This is the list of all modules currently available in the repository. `AvailableModule` is a helper class that tracks module versions. This allows multiple versions of a single mod to co-exist in the repository. A call to `AvailableModule.Latest(KSPVersion kspVersion)` will produce a CkanModule compatible with the given KSP version (or throw if no such version exists)
* `installed_modules` tracks all CKAN installed modules. It uses the `InstalledModule`([InstalledModule.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/InstalledModule.cs)) class which contains a list of installed files and a `Module` instance.

#### RegistryManager ([RegistryManager.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/RegistryManager.cs))
* Singleton, use RegistryManager.Instance()
* Holds the current registry and provides methods for serialization
* In most cases you will need the `registry` field which is the current `Registry`

####  ModuleInstaller ([ModuleInstaller.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/ModuleInstaller.cs))
* Singleton, use ModuleInstaller.Instance
* Contains all the logic for installing, updating and uninstalling modules
* Important public functions are `IsCached`, `InstallList`, `Uninstall`
* `InstallList` is what the clients call to install mods. It resolves dependencies according to `RelationshipResolverOptions`, downloads the data and performs the necessary file operations. 
* `Uninstall` will automatically uninstall mods that depend on whatever you're uninstalling unless you explicitly tell it not to.

#### KSP

#### Net

#### RelationshipResolver

### RelationshipResolverOptions

#### Repo

#### FilesystemTransaction ([TransactionalFilesystem.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/TransactionalFilesystem.cs))
* Enables filesystem operations in an atomic fashion
* Allows us to manipulate files without worrying about incomplete changes due to run- time errors.
* You can use it by instantiating a new `FilesystemTransaction` object, then using its methods `OpenFileWrite`, `RemoveFile`, `CreateDirectory`, `DeleteDirectory`. To actually apply all the operations, call `Commit()`. It's useful to `Rollback()` if you're sure to roll back a transaction, but it's not necessary, not calling `Commit()` is enough to be sure you haven't done any damage to the user's files
* `OpenFileWrite` returns a `TransactionalFileWriter` object, you can use its `Stream` property to get a `FileStream` instance which you can write to.
* Isn't actually atomic, most filesystems and/ or hardware don't support it. It just queues all operations and then executes them at once on `Commit()`

#### User ([User.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CKAN/User.cs))
* The User class allows interaction with the User from core code
* Works in both the command-line and GUI clients 
* `WriteLine` displays a message to the user
* `YesNo` displays a message and a Yes/No prompt
* `Error` displays an error
* Use sparingly, popping up 20 dialogs at once in the GUI is not fun for the user :(

### Logging

We use log4net for logging. 

### Command-line client

The command-line client resides in [CmdLine/Main.cs](https://github.com/KSP-CKAN/CKAN/blob/master/CKAN/CmdLine/Main.cs). This is the place to look if you wish to know how a particular feature works or if you're just starting to read the code.

### GUI client
The GUI client is sadly much more complicated than the command-line one, mostly because 90% of it is interface code with WindowsForms and the window designs themselves. The GUI is done in Visual Studio and although there are other WindowsForms editors, they have not been tested with CKAN (tell us if they work :P). 

### CKAN Tools

## Setting up your development environment

### Linux- based operating systems

You will need at least [MonoDevelop](http://monodevelop.com/) and [Mono](http://www.mono-project.com/) to be able to open and build the CKAN.sln file. You can use `xbuild` that comes with Mono for command-line builds. MonoDevelop aims to be a full C#- capable IDE similarly to Visual Studio as comes with an editor, compiler and debugger.

### Windows

You most likely want to use [Visual Studio](http://www.visualstudio.com/). Although proprietary, the [Express version](http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx) is fully capable (same compiler as the other versions) and comes at zero cost. You can use MonoDevelop on Windows, but it is a worse IDE than VS (for now).

First thing you will have to do is [fork](https://help.github.com/articles/fork-a-repo/) CKAN's [master branch](https://github.com/KSP-CKAN/CKAN). 

## General workflow

#### Creating pull requests

#### Merging pull requests into master

#### Issues

#### Coding style & formatting

In general, for the C# code we follow [the Microsoft C# Coding Conventions](http://msdn.microsoft.com/en-us/library/ff926074.aspx).

Additionaly for readability and/ or performance reasons we avoid:
* [LINQ](http://msdn.microsoft.com/en-us/library/bb397926.aspx)
* [dynamic](http://msdn.microsoft.com/en-us/library/dd264736.aspx)

## Communication with other contributors

All contributors and fans hang out in #ckan @ irc.esper.net

Come and talk to us :)

If you don't have an IRC client installed - [here is one that runs in your browser](https://kiwiirc.com/client).
