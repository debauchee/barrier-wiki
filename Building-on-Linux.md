## Binaries Built

- `barrier` the GUI front-end and "main" program
- `barriers` the CLI server component
- `barrierc` the CLI client component

**note**: these aren't the only things build at compile time, but merely what the end-user is expected to use.

## Preparing your system

### Ubuntu/Debian/Raspbian

```shell
sudo apt update && sudo apt upgrade
sudo apt install git cmake make xorg-dev g++ libcurl4-openssl-dev \
                 libavahi-compat-libdnssd-dev libssl-dev libx11-dev \
                 libqt4-dev qtbase5-dev
```

### Fedora

```shell
sudo dnf install git cmake make gcc-c++ xorg-x11-server-devel \
                 libcurl-devel avahi-compat-libdns_sd-devel \
                 libXtst-devel qt5 qt5-devel openssl-devel
```

### CentOS 8+

(Make sure you have RPM Fusion and EPEL enabled, with PowerTools as well)

```shell
sudo dnf groupinstall "Development Tools"
sudo dnf install 'dnf-command(config-manager)'
sudo dnf config-manager --set-enabled PowerTools
sudo dnf install cmake3 avahi-compat-libdns_sd-devel libX11-devel libXtst-devel \
                qt5-qtbase-devel libcurl-devel desktop-file-utils openssl-devel
```

### openSUSE

```shell
sudo zypper install --type pattern devel_basis
sudo zypper install libdrm-devel libglvnd-devel libICE-devel \
        libQt5Core-devel libQt5Gui-devel libQt5Network-devel \
        libqt5-qtbase-common-devel libQt5Widgets-devel libSM-devel \
        libXinerama-devel libXrandr-devel libXrender-devel
```

## Building from source
```shell
git clone https://github.com/debauchee/barrier.git
# this builds from master,
# you can get release tarballs instead
# if you want to build from a specific tag/release
cd barrier
git submodule update --init --recursive
./clean_build.sh
cd build
sudo make install # install to /usr/local/
```