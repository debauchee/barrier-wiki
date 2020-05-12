# Overview

## Before You Start

You should be at least moderately comfortable with Git, CMake, and the command
line before relying on this method. It requires a good bit of free hard drive 
space due to the large amount of dependencies required for the build (see below)
and is meant for programmers that want to help discover and fix programming 
errors. When in doubt go for [binaries](Home) instead!

# How to Build Barrier.app on MacOS

*as of May 2020*

- Install Xcode from the App Store or download and install it 
from the [Apple Developer Website](https://developer.apple.com/download/more/?=xcode)
- Install either [MacPorts](https://www.macports.org/) or [Homebrew](https://brew.sh/) 
following the instructions on their websites
- Install dependencies with either MacPorts or Homebrew (see below)
- Build either the `Debug` or the `Release` targets (see below)

### Install dependencies with MacPorts

  - Install packages for Qt5, OpenSSL, and PkgConfig
  ```
  sudo port install qt5 openssl pkgconfig
  ```
  
### Install dependencies with Homebrew

  - Install packages for Qt5, OpenSSL, and pkgconfig
  ```
  brew install qt5 openssl pkg-config
  ```
  
### Clone the repository

  ```
  git clone https://github.com/debauchee/barrier
  ```
  - Change directories into the cloned repository
  ```
  cd barrier
  ```
 
### Build the Debug target
  *The debug target produces un-stripped binaries that have symbols that can be 
  used for debugging.*

  - Build the `Debug` target using the `clean_build.sh` script
  ```
  ./clean_build.sh
  ```
  
  By default the resulting binaries will be in `./build/bin` and the 
  application bundle (.app) will be in `./build/bundle`. The debug target
  does not create a disk image (dmg).

### Build the Release target
  *The release target produces smaller stripped binaries and distributable disk image. 
  This is probably what you want if you are a user.*
  
  - Build the `Release` target using the `clean_build.sh` script with environment variables
  
  *This step assumes the use of a bourne-like shell (i.e. bash). If using another shell like*
  `csh` *change the export command to use* `setenv` *instead*.
  ```
  export B_BUILD_TYPE=Release
  ./clean_build.sh
  ```
  
  By default the resulting binaries will be in `./build/bin` and the 
  application bundle (.app) will be in `./build/bundle`. The release target
  also creates a disk image (dmg).
  
#### Notes
  
  A [`build_env.sh`](https://github.com/debauchee/barrier/blob/b6a1b5742006157c3fe746288961a9d2827a3f26/clean_build.sh#L20)
  file can be created for persistent build environment variables [without being tracked by git](https://github.com/debauchee/barrier/blob/b6a1b5742006157c3fe746288961a9d2827a3f26/.gitignore#L1)
  
