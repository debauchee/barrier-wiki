# Overview

## Before You Start

You should be at least moderately comfortable with Git, CMake, and the Windows command prompt before relying on this method. It requires a good bit of free hard drive space due to the large amount of dependencies required for the build (see below) and is meant for programmers that want to help us discover and fix programming errors.  When in doubt go for [binaries](Home) instead!

# How to Build Barrier under Windows

*as of 08-18-2019*

I am using Windows 10 64-Bit English 1903

- install [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)
  - Select Desktop development with C++
- install [CMake](https://cmake.org/download/)
- install [Qt (Open Source)](https://www.qt.io/download)
    - download the installer
    - on the select components screen, check the Qt version you want, I used `5.13.x`, `x` being the latest.
- install [Bonjour SDK for Windows](https://developer.apple.com/bonjour/)
    - requries an account to download
    - *warning:* will install into `Program Files` regardless
- download the Barrier source code
    - either clone it via Git
    - or download the release archive and extract it
- edit lines 4--8 in `clean_build.bat` accordingly, these are mine:

      set B_BUILD_TYPE=Debug
      set B_QT_ROOT=E:\dev\Qt
      set B_QT_VER=5.13.0
      set B_QT_MSVC=msvc2017_64
      set B_BONJOUR=C:\Program Files\Bonjour SDK

- open a *Developer Command Prompt for VS 2019*
    - Start > Programs > Visual Studio 2019 > Visual Studio Tools > Developer Command Prompt for VS 2019
- navigate to the Barrier folder
- invoke `clean_build.bat`

If everything went well you should now have a working `barrier.exe` inside `build/bin/Debug`.
I don't know whether a release build built this way is deployable.

## Customization

You will likely need to tweak a few knobs to complete the build. The easiest way to override build paths locally is to create a file called winbuild_env.bat in the root of your Barrier tree. This file is sourced before any build commands are executed so it's a good place to put environmental overrides. The most common ones are B_QT_ROOT to tell the build where your MSVC Qt folder is, and B_BUILD_TYPE when you're ready for your Release build.

    set B_QT_ROOT=E:\Qt\5.13.0\msvc2017_64

    REM Uncomment when ready for a release build
    REM set B_BUILD_TYPE=Release

## What's next?

### Local changes

Feel free to make whatever changes you like to your local copy of Barrier. Barrier is open-source and licensed under the GPL so as long as you abide by the [license](https://raw.githubusercontent.com/debauchee/barrier/master/LICENSE) then you're completely fine. Have fun!

### Submitting patches

If you make a change to Barrier's source that you think would benefit others, fork the project on GitHub and submit a pull request. If we agree that the patch would improve Barrier for a wide range of users then we may merge it. At this point it will have become an "official" change and you'll be credited in the commit log. Isn't open-source wonderful? :)