# Overview

## Before You Start

You should be at least moderately comfortable with Git, CMake, and the Windows command prompt before relying on this method. It requires a good bit of free hard drive space due to the large amount of dependencies required for the build (see below) and is meant for programmers that want to help us discover and fix programming errors.  When in doubt go for [binaries](Home) instead!

## Requirements

To build Barrier on Windows you'll need, at minimum, the following things:
- [Microsoft Visual Studio](https://www.visualstudio.com/vs/community/): The free version of VS 2017 will suffice. It will eventually require you to create an MSDN account.
- [Bonjour SDK](https://developer.apple.com/download/more/?=Bonjour%20SDK%20for%20Windows): You'll need to make a free Apple Developer account first if you don't already have one to begin the download.
- [CMake](https://cmake.org/download/): Get the Windows x64 MSI or ZIP.
- [Git](https://git-scm.com/download/win): They automatically start the 32-bit download so cancel the 32 and click the 64-bit link.
- [Qt](https://www.qt.io/download-thank-you?os=windows): The 5.9 branch is stable with Barrier.
- [WiX Toolset](https://github.com/wixtoolset/wix3/releases/): This is specifically for building the Barrier installation project, but building Barrier with the default configuration ill fail if it isn't available.

## Procedure

### The Barrier Program
- Clone the Barrier repository from GitHub
- Customize winbuild_env.bat (see below)
- Run clean_build.bat
- If the build succeeds your binaries will be in build/bin/{Debug,Release}

### The Barrier Installer
*You will need to modify the build script if you want anything other than a Release installer for Win64.*
- Complete the build procedure above using the Release configuration
- Run build_installer.bat

## Customization

You will likely need to tweak a few knobs to complete the build. The easiest way to override build paths locally is to create a file called winbuild_env.bat in the root of your Barrier tree. This file is sourced before any build commands are executed so it's a good place to put environmental overrides. The most common ones are B_QT_ROOT to tell the build where your MSVC Qt folder is, and B_BUILD_TYPE when you're ready for your Release build.

    set B_QT_ROOT=E:\Qt\5.6.3\msvc2015_64

    REM Uncomment when ready for a release build
    REM set B_BUILD_TYPE=Release

## What's next?

### Local changes

Feel free to make whatever changes you like to your local copy of Barrier. Barrier is open-source and licensed under the GPL so as long as you abide by the [license](https://raw.githubusercontent.com/debauchee/barrier/master/LICENSE) then you're completely fine. Have fun!

### Submitting patches

If you make a change to Barrier's source that you think would benefit others, fork the project on GitHub and submit a pull request. If we agree that the patch would improve Barrier for a wide range of users then we may merge it. At this point it will have become an "official" change and you'll be credited in the commit log. Isn't open-source wonderful? :)