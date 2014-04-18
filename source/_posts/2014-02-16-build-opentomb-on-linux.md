---
layout: post
title: "Build OpenTomb on Linux"
date: 2014-02-16 05:18:22 +0100
updated: 2014-02-16
comments: true
tags: [Linux, OpenTomb]
categories: 
---

A while ago the [OpenTomb](https://sourceforge.net/p/opentomb) [developers](http://www.tombraiderforums.com/showthread.php?t=197508) accepted my contribution that allowed the engine to be compiled under Linux.
This was not too hard, since the engine only depends on cross-platform libraries and uses GCC to [compile on Windows](http://www.moddb.com/games/opentomb/tutorials/how-to-compile-opentomb-in-windows-using-codeblocks-ide). It mainly consisted of a CMake script and a few code fixes.
The [CMake script](https://sourceforge.net/p/opentomb/code/ci/default/tree/CMakeLists.txt) already contains a very brief explanation on how to compile on the command-line.  

This post provides a more detailed step-by-step guide to get everything up and running for development on Linux.

*Tested with:*

+ Arch Linux
+ Latest [OpenTomb commit](https://sourceforge.net/p/opentomb/code) as of 20140216
+ Latest config files from [OpenTomb binary archive](http://sourceforge.net/projects/opentomb/files/engine.7z/download)

*Requirements:*

+ SDL2 installed _(tested with 2.0.1)_
+ zlib installed _(tested with 1.2.8)_
+ Mercurial installed _(tested with 2.9)_
+ CMake 2.8 installed _(tested with 2.8.12)_
+ QtCreator installed _(tested with 3.0.1)_
+ Game assets of TombRaider 1-5 _(tested with [GOG](http://www.gog.com/game/tomb_raider_123) [version](http://www.gog.com/game/tomb_raider_the_last_revelation_chronicles))_

### Get the source code

Choose a directory and clone the OpenTomb source code repository:  
`hg clone http://hg.code.sf.net/p/opentomb/code opentomb-code`

### Compile using QtCreator

1. Start up QtCreator and select `File -> Open File or Project`.
2. Navigate to your cloned repository directory and open `CMakeLists.txt`.
{% img /images/build_opentomb/build_opentomb_01.jpeg 'Opening CMakeLists.txt' %}
3. Choose a build directory. Make sure it is located outside of the repository. The default build directory suggested by QtCreator (e.g. `opentomb-code-build`) is usually just fine.
{% img /images/build_opentomb/build_opentomb_02.jpeg 'Choosing a build directory' %}
4. Provide CMake with the arguments `-DCMAKE_BUILD_TYPE=Debug` (if you plan to develop) or `-DCMAKE_BUILD_TYPE=Release` (if you just want to run the engine).
5. Select `Unix Generator` as the generator and click `Run CMake`. This should successfully create a Makefile if the required libraries are installed. Click `Finish`.
{% img /images/build_opentomb/build_opentomb_03.jpeg 'Configuring CMake' %}
6. Hit `Ctrl+B` to start compilation. This can take a while to complete and creates the OpenTomb executable inside the build directory.
{% img /images/build_opentomb/build_opentomb_04.jpeg 'QtCreator compiles' %}

#### Pro tip: faster compilation

Add `-j2` or `-j4` (depending on how many CPU cores you have) into `Projects -> Build & Run -> Build Steps -> Details -> Additional arguments`.
{% img /images/build_opentomb/build_opentomb_08.jpeg 'Configure parallel compilation jobs' %}

### Add config files

Extract the files form the OpenTomb binary archive (engine.7z) into the build directory. The important files/directories are: data, save, scripts, VeraMono.ttf, ascII.txt, config.lua.
{% img /images/build_opentomb/build_opentomb_05.jpeg 'OpenTomb config files' %}

### Add game assets

Copy game files from the original Tomb Raider games into the corresponding `data` subdirectory like you would on Windows. E.g. copy all "*.TR2" levels from the Tomb Raider 2 data directory into `opentomb-code-build/data/tr2/data`.
To get audio, copy `MAIN.SFX`, too.


### Final touch

Modify `cvars.game_level` in config.lua to match an existing level file, e.g. `data/tr2/data/WALL.TR2`. Keep in mind, that file paths in Linux are case sensitive.
{% img /images/build_opentomb/build_opentomb_06.jpeg 'Configure startup game level' %}

Now you should be able to launch the game using the file browser or from within QtCreator via `Ctrl+R`.
{% img /images/build_opentomb/build_opentomb_07.jpeg 'Running OpenTomb' %}
