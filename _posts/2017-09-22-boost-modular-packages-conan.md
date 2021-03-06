---
layout: post
title: 'Boost 1.64.0 - Modular Packages on Conan.io'
tags: [C++, Conan.io, Bintray, Boost]
---

Starting with Boost 1.64.0, the Bincrafters team will now publish each of the Boost C++ libraries as separate packages on Conan.io. With this, developers can now reference the specific Boost libraries they want to use as dependencies rather than referencing the entire Boost collection. 

## Before
```
[requires]
Boost/1.64.0@conan/stable
```

## After
```
[requires]
Boost.Process/1.64.0@bincrafters/stable
```
 
---

## Technical Information
To get started with using the Bincrafters public Conan repository, please see this post: [Bincrafters Conan Instructions](https://bincrafters.github.io/2017/06/06/using-bincrafters-conan-repository)

## Conan Version
It is very highly recommended that you get the latest version of Conan.io 0.27.0 or higher to use these packages.  

	$ pip install conan --upgrade

## Package Options
Conan allows users to pass options to for each package upon install. The list of options for Boost packages are: 

|Package        |Option Name		| Default Values   | Possible Value    
|---------------|-------------------|-------------------|------------------
|All				 |shared				| False                | True/False         
|Python		 |python				| "python"          | string/path to local python install 
|Iostreams	 |use_zlib				| True                | True/False         
|Iostreams	 |use_bzip2			| True                | True/False  
|Regex			 |use_icu				| True                | True/False  

*Note: The default option for "python" will search environment variables for a python install, and should find it by default on most systems*

## Boost Library Inter-dependencies
The Conan recipe for each Boost library naturally defines each of it's direct dependencies. Conan is therefor able to perform normal transitive dependency resolution just like any other package, only downloading the required packages to your local cache, and only passing the required library information to your build system for including and linking.  Properly expressing the Boost dependency tree in Conan was a significant part of the effort involved in this project, and a major milestone. Special thanks to Peter Dimov for providing this awesome [Boost Dependency Report](https://pdimov.github.io/boostdep-report/master/module-levels.html)

## Boost Library Cyclic Dependencies
Those who have worked deeply with Boost are likely aware that within the libraries exist 4 sets of cyclic dependencies.  That is, groups of libraries where two-or-more depend on each other in a way which forms a loop.  This was certainly one of the challenges which has historically prevented Boost from being modularized previously.  Package managers like Conan do not support cyclic dependencies, so to work around this issue we've created special metapackages to encapsulate the loop.  For example, to build `Locale`, B2 required that `Date_Time`, `Pool`, `Serialization`, `Spirit`, and `Thread` all be present.  

We wanted to explain the situation here for the curious, and because you may see things like `Boost.Level8group@bincrafters/stable` in your Conan logs.  However, to be clear: **users don't need to know about the metapackages or reference them directly.**  Users should only reference the libraries they want, and let Conan take care of the rest. 

## Conan Download and Cache Explained
If the Bincrafters repository contains a precompiled binary that matches your OS, compiler, and compiler settings, Conan will just download it to your local conan cache, and use it.  If it doesn't, Conan will download the recipe, build from source, and store the binary in your cache. Then, any future projects on that machine which use the same library (and the same compiler settings), will just use the binary in the cache.  This caching and re-use of compiled libraries is a cornerstone of Conan's efficiency benefits. 

## Continuous Integration
Bincrafters uses Appveyor and Travis CI to provide CI for all our packages.  This populates our Conan repository with not only the package recipes (which contain the build instructions), but also a wide array of pre-compiled Binaries for the most common operating systems, compilers, and settings.  The Conan team maintains a project called ["Conan Package Tools"](https://github.com/conan-io/conan-package-tools), which streamlines the CI process for us, making it much easier to maintain. 

## About the Project

### The First Release
Boost 1.64.0 is the first release of these packages, and took significant effort from multiple developers over the past three months.  We are very interested in continuing to improve the packages over time, and hope community members will notify us of problems or areas for improvement.  For specific issues relating to a single package, you can find the github repository which contains the conan recipe on the corresponding Bintray page for that package.  Please visit this Github repository and report the issue there.  For more general questions, please feel free to email us: bincrafters at g mail dot com .

### Boost Background
The Boost libraries are some of the most significant and widely-used open-source C++ libraries.  They have historically been released in the form of a single package which contains all of the libraries bundled together.  This provides a number of benefits, but also has significant and well-known disadvantages. For example, one of the oldest and most common requests from the Boost user community has been the ability to consume individual libraries from Boost without getting the entire bundle, something which has always been unsupported. While the Boost team is currently working toward changing this paradigm to something more modular, it will be some time until that effort is complete. Until then, Bincrafters will be publishing each release of the Boost libraries as modular pacakges on our public Conan repository.

### Conan.io Background
Conan.io provides a toolset and platform which is uniquely equipped to create and publish the Boost libraries as separate modular packages. The Bincrafters team felt this was a great opportunity, and decided to pursue it. Separating the Boost libraries into packages was difficult, however the flexibility and power of Conan made it not only possible, but also maintainable, which was a key concern at the start of the project.

## Disclaimers
Prior to using Bincrafters packages, please see the [Bincrafters Disclaimer Page](https://bincrafters.github.io/2017/05/01/bincrafters-package-disclaimers/). 