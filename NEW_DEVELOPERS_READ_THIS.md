This document aims to explain the inner workings of CKAN. Hopefully, this will reduce friction for new developers entering CKAN development. Also, this document provides some level of insurance that knowledge of the codebase won't be lost when a developer leaves CKAN development.

As with anything CKAN, this document is always open to changes and improvements. Be aware that any contribution should follow the [Contribution guidelines](https://github.com/KSP-CKAN/CKAN/blob/master/CONTRIBUTING.md) and the [Code of Conduct](https://github.com/KSP-CKAN/CKAN/wiki/Code-of-Conduct).

### The CKAN project

In its essence, CKAN consists of a core library module ("Core"), a command-line client and a GUI client - collectively referred to as "CKAN" and a large number of supporting tools such as parsers, validators and bots ("CKAN Tools", "the tools" or "the toolset").

While CKAN itself is written exclusively in C# .NET, the toolset sports a variety of programming languages such as Perl, bash and others. Contributions to CKAN are expected to be in C#, use .NET 4.0 and be compatible with both Mono (Win32, Linux, MacOSX) and Visual Studio (Win32) environments. There is no such limitation present when contributing to the tools, they can be in any language and platform as long as it is [FOSS](http://en.wikipedia.org/wiki/Free_and_open-source_software) and is available on all of our development targets - modern Linux- based and Windows operating systems.
 
 
