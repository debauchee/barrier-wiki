## Ubuntu/Debian/Raspbian: Build from source

```
sudo apt update && sudo apt upgrade
sudo apt install git cmake make xorg-dev g++ libcurl4-openssl-dev \
                 libavahi-compat-libdnssd-dev libssl-dev libx11-dev \
                 libqt4-dev qtbase5-dev
git clone git@github.com:debauchee/barrier.git
cd barrier
./clean_build.sh
cd build
sudo make install  # installs to /usr/local/ 
```