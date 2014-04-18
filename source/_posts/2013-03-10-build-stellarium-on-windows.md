---
layout: post
title: "Build Stellarium on Windows"
date: 2013-03-10 13:54:47
updated: 2013-03-10
comments: true
tags: [Windows, Stellarium]
categories:
---

### How to build Stellarium on Windows

*Tested with:*

+ Windows 7 32Bit
+ Windows 8 32Bit
+ the source tarball for Stellarium 0.12.0
+ the current Stellarium in-development branch 0.12.1

*Requirements:*

+ cygwin with bazaar package installed
+ no random crap inside the PATH variable that interferes with CMake, MinGW, Qt


#### [MinGW (20120426)](http://sourceforge.net/projects/mingw/files/Installer/mingw-get-inst/mingw-get-inst-20120426/mingw-get-inst-20120426.exe/download):

Run the installer and select:

+ *Pre-packaged repository catalogues* (this will install gcc 4.6, while selecting *Latest catalogue* will install gcc 4.7 and the stellarium executable will crash at start)
+ *C*
+ *C++*
+ *MSYS Basic System*

Start MSYS Shell (*MINGW_INSTALL\msys\1.0\msys.bat*) and run:  
`mingw-get install libz libiconv gettext`

Do not mind errors about already installed packages.


#### [QT 4.8.4](http://releases.qt-project.org/qt4/source/qt-win-opensource-4.8.4-mingw.exe):

Install Qt into a path without spaces.  
When asked by the installer point it to the previously installed MinGW directory.


#### [CMake 2.8.10](http://www.cmake.org/files/v2.8/cmake-2.8.10.2-win32-x86.exe):

Install CMake. Do not add it to the PATH variable when asked for at the end of the installation.


#### [Stellarium source code](https://code.launchpad.net/~stellarium/):

Create a folder `stellarium`. This is where we will put the source code, the build directory and QtCreator.

Start cygwin and go into the created folder. Fetch the latest source:  
`bzr branch lp:stellarium stellarium-lp`

If the above command results in some sort of certificate error try this:  
`bzr branch -Ossl.ca_certs=/usr/ssl/certs/ca-bundle.crt lp:stellarium stellarium-lp`


#### [QtCreator 2.6.2](http://releases.qt-project.org/qtcreator/2.6.2/sources/qt-creator-windows-opensource-2.6.2.7z):

Extract the archive into its own folder inside the `stellarium` directory next to `stellarium-lp`.  
Inside `stellarium` folder create a new file `qtcreator.bat` with following content:

```
@echo off

set PATH=%PATH%;C:\MinGW_x86\bin
set PATH=%PATH%;C:\MinGW_x86\include
set PATH=%PATH%;C:\MinGW_x86\lib

set PATH=%PATH%;C:\Qt\4.8.4
set PATH=%PATH%;C:\Qt\4.8.4\bin
set PATH=%PATH%;C:\Qt\4.8.4\include
set PATH=%PATH%;C:\Qt\4.8.4\lib

start qt-creator\bin\qtcreator.exe
```

Change the paths accordingly.


#### Build:

Start `qtcreator.bat`.  
Check if a MinGW Build Kit does exist: *Tool -> Options -> Bild & Run -> Kits*. Create a new one if it does not.

Open the Stellarium project using *File -> Open File or Project* and select `stellarium-lp\CMakeLists.txt`.
Go with the default build directory when asked for it (should end with `stellarium-lp-build`).
Specify the `cmake` executable if asked for it.
Select the `MinGW Generator` and `Run CMake`.

Compile.


#### Run:

To run from inside QtCreator change the working directory of the *Run* settings from `stellarium-lp-build\src` to the source folder `stellarium-lp`.

Run.


#### Debug:

The default CMake configuration of the stellarium source from launchpad is already set to `Debug`.

