
## Ubuntu build sequence for CubicSDR with SDRPlay RSPdx
```
# directory structure
 sdrplay
   + SoapySDRPlay
   + SoapyRemote
   + SoapySDR
   + wxWidgets-3.1.3
   + wxWidgets-staticlib
   + CubicSDR

# build dependencies
 CubicSDR
 + SoapySDR
   + SoapyRemote
   + SoapySDRPlay
     + API-linux

```

### Ubuntu packages
```
> apt-get install git build-essential automake cmake g++ swig
> apt-get install libgtk2.0-dev libpulse-dev libpython-dev python-numpy
> apt-get install mesa-utils libeglw1-mesa libglw1-mesa-dev
> apt-get install freeglut3-dev freeglut3
```


## SDRPlay Linux Driver API
```
# Linux driver:
# https://www.sdrplay.com/downloads/https://www.sdrplay.com/downloads/
# Select: Software, linux, API/HW DRIVER ? V3.06
# https://www.sdrplay.com/linuxdl2.phphttps://www.sdrplay.com/linuxdl2.php
#  download: SDRplay_RSP_API-Linux-3.07.1.run

# as root
> ./SDRplay_RSP_API-Linux-3.07.1.run
...
Installing /usr/local/lib/libsdrplay_api.so.3.07...Done
Installing header files in /usr/local/include...Done
Installing API Service in /usr/local/bin...Done
Installing Service scripts and starting daemon...Created symlink from /etc/systemd/system/multi-user.target.wants/sdrplay.service to /etc/systemd/system/sdrplay.service.

Service has been installed and started. This device should be rebooted
before the service is used. After the installation script finishes,
reboot this device.
 
To start and stop the API service, use the following commands...
 
sudo systemctl start sdrplay
sudo systemctl stop sdrplay
 
SDRplay API 3.07 Installation Finished
 
Would you like to add SDRplay USB IDs to the local database for easier
identification in applications such as lsusb? [y/n] y
This script will add the SDRplay IDs to the local USB database
and will require sudo permissions. Enter your password if prompted
 
Now when you type lsusb you will see the correct SDRplay device type
SDRplay IDs added. Try typing lsusb with an SDRplay device connected.
If the USB IDs get updated from the central reprository, just type
the following command to add the SDRplay devices again...
 
sdrplay_usbids.sh
 
Installation Finished

```

### Check USB connectivity
Watch system logs and attached the SDRPlay RSPdx:
```
> journalctl -f
...
May 16 11:58:28 ds-ubuntu kernel: usb 1-2: new high-speed USB device number 9 using xhci_hcd
May 16 11:58:28 ds-ubuntu kernel: usb 1-2: New USB device found, idVendor=1df7, idProduct=3030
May 16 11:58:28 ds-ubuntu kernel: usb 1-2: New USB device strings: Mfr=0, Product=0, SerialNumber=0


> lsusb
...
Bus 001 Device 005: ID 1df7:3030 SDRplay RSPdx

```

## Soapy SDR

### Soapy SDRPlay

```
# https://github.com/pothosware/SoapySDRPlay
# https://github.com/pothosware/SoapySDRPlay/wiki

> git clone https://github.com/pothosware/SoapySDRPlay.git
> cd SoapySDRPlay

# For the newer SDPPlay RSPdx - use branch: api3
> git checkout api3
> git branch -a
* api3
  master
> mkdir build; cd build
> cmake ..
..
-- Build type not specified: defaulting to release.
-- Found libsdrplay: /usr/local/include, /usr/local/lib/libsdrplay_api.so
-- LIBSDRPLAY_INCLUDE_DIRS - /usr/local/include
-- LIBSDRPLAY_LIBRARIES - /usr/local/lib/libsdrplay_api.so
-- Performing Test HAS_STD_CXX11
-- Performing Test HAS_STD_CXX11 - Success
-- Found Git: /usr/bin/git (found version "2.7.4") 
-- Module sdrPlaySupport configured with version: 0.3.0-86c29ad
..

> make
..
[100%] Linking CXX shared module libsdrPlaySupport.so
[100%] Built target sdrPlaySupport

> make install
...
-- Installing: /usr/local/lib/SoapySDR/modules0.8/libsdrPlaySupport.so

```

### Soapy SDR Remote

```
# SoapyRemote
> git clone https://github.com/pothosware/SoapyRemote.git
> cd SoapyRemote
> mkdir build; cd build
> cmake ..
...
-- SoapyRemote version: v0.5.2-g6d9bd820
-- Install prefix: /usr/local
-- Configuring done
-- Generating done
-- Build files have been written to: /home/duane/sdrplay/SoapyRemote/build

> make
...
[100%] Built target SoapySDRServer

# as root
> make install
...
-- Installing: /usr/local/lib/SoapySDR/modules0.8/libremoteSupport.so
-- Set runtime path of "/usr/local/lib/SoapySDR/modules0.8/libremoteSupport.so" to ""
-- Installing: /usr/local/bin/SoapySDRServer
-- Set runtime path of "/usr/local/bin/SoapySDRServer" to ""
-- Up-to-date: /usr/local/share/man/man1/SoapySDRServer.1
-- Installing: /usr/local/lib/systemd/system/SoapySDRServer.service
-- Up-to-date: /usr/local/lib/sysctl.d/10-SoapySDRServer.conf

```

### SoapySDR

```
> git clone https://github.com/pothosware/SoapySDR.git

> cd SoapySDR
> mkdir build; cd build
> cmake ..
-- ######################################################
-- ## SoapySDR enabled features
-- ######################################################
-- 
 * Library , runtime library v0.8.0-gf722f9ce
 * Apps , command line applications
 * Tests , library unit tests
 * Python , python bindings v2.7.12
 * Python3 , python3 bindings
...
-- SoapySDR version: v0.8.0-gf722f9ce
-- ABI/so version: v0.8
-- Install prefix: /usr/local
-- Configuring done
-- Generating done
-- Build files have been written to: /home/duane/sdrplay/SoapySDR/build

> make 
...
[ 55%] Linking CXX shared library libSoapySDR.so
[ 55%] Built target SoapySDR
Scanning dependencies of target SoapySDRUtil
[ 57%] Building CXX object apps/CMakeFiles/SoapySDRUtil.dir/SoapySDRUtil.cpp.o
[ 60%] Building CXX object apps/CMakeFiles/SoapySDRUtil.dir/SoapySDRProbe.cpp.o
[ 62%] Building CXX object apps/CMakeFiles/SoapySDRUtil.dir/SoapyRateTest.cpp.o
[ 65%] Linking CXX executable SoapySDRUtil
[ 65%] Built target SoapySDRUtil
...

# as root
> make install
...
-- Installing: /usr/local/bin/SoapySDRUtil

> ldconfig

```
### Verify SDRPlay RSPdx connectivity

```

> SoapySDRUtil --info
######################################################
##     Soapy SDR -- the SDR abstraction library     ##
######################################################

Lib Version: v0.8.0-gf722f9ce
API Version: v0.8.0
ABI Version: v0.8
Install root: /usr/local
Search path:  /usr/local/lib/SoapySDR/modules0.8
Module found: /usr/local/lib/SoapySDR/modules0.8/libremoteSupport.so  (0.5.2-6d9bd82)
Module found: /usr/local/lib/SoapySDR/modules0.8/libsdrPlaySupport.so (0.3.0-86c29ad)
Available factories... remote, sdrPlay
Available converters...
 -  CF32 -> [CF32, CS16, CS8, CU16, CU8]
 -  CS16 -> [CF32, CS16, CS8, CU16, CU8]
 -  CS32 -> [CS32]
 -   CS8 -> [CF32, CS16, CS8, CU16, CU8]
 -  CU16 -> [CF32, CS16, CS8]
 -   CU8 -> [CF32, CS16, CS8]
 -   F32 -> [F32, S16, S8, U16, U8]
 -   S16 -> [F32, S16, S8, U16, U8]
 -   S32 -> [S32]
 -    S8 -> [F32, S16, S8, U16, U8]
 -   U16 -> [F32, S16, S8]
 -    U8 -> [F32, S16, S8]

> SoapySDRUtil --probe
######################################################
##     Soapy SDR -- the SDR abstraction library     ##
######################################################

Probe device 
[INFO] devIdx: 0
[INFO] hwVer: 4
[INFO] mode: 0

----------------------------------------------------
-- Device identification
----------------------------------------------------
  driver=SDRplay
  hardware=200104C742
  sdrplay_api_api_version=3.070000
  sdrplay_api_hw_version=4
...
```

## CubicSDR

Original build instructions: [https://github.com/cjcliffe/CubicSDR/wiki/Build-Linux]


### liquid-dsp

```
# Build liquid-dsp
> git clone https://github.com/jgaeddert/liquid-dsp
> cd liquid-dsp
> ./bootstrap.sh
> ./configure --enable-fftoverride 
...
configure: creating ./config.status
config.status: creating makefile
config.status: creating config.h
...

> make 
...
ranlib libliquid.a
gcc -g -O2  -msse4.1  -Wall -fPIC  -shared -Xlinker -soname=libliquid.so -o libliquid.so -Wl,-whole-archive libliquid.a -Wl,-no-whole-archive -lfftw3f -lm -lc 

# as root
> make install
...
mkdir -p /usr/local/lib
mkdir -p /usr/local/include/liquid
install -m 644 -p libliquid.a libliquid.so /usr/local/lib
install -m 644 -p include/liquid.h /usr/local/include/liquid

---------------------------------------------------------
  liquid-dsp was successfully installed.     
...

> ldconfig

```

### wxWidgets

wxWidgets [https://www.wxwidgets.org/] builds a graphical library, in this case located: sdrplay/wxWidgets-staticlib.

```
> wget https://github.com/wxWidgets/wxWidgets/releases/download/v3.1.3/wxWidgets-3.1.3.tar.bz2
> tar -xvjf wxWidgets-3.1.3.tar.bz2 
> cd wxWidgets-3.1.3/
> export WDIR=$(realpath ../wxWidgets-staticlib)
# WDIR will also be used in the CubicSDR build
> mkdir -p $WDIR
> ./configure --with-opengl --disable-shared --enable-monolithic --with-libjpeg --with-libtiff --with-libpng --with-zlib --disable-sdltest --enable-unicode --enable-display --enable-propgrid --disable-webkit --disable-webview --disable-webviewwebkit --prefix=$WDIR CXXFLAGS="-std=c++0x"
...
Libtiff is now configured for x86_64-pc-linux-gnu

  Installation directory:             /home/duane/sdrplay/wxWidgets-staticlib
  Documentation directory:            ${prefix}/share/doc/tiff-4.0.10
  C compiler:                         gcc -pthread -Wall -Wundef -O2 -pthread -I/usr/include/gtk-unix-print-2.0 -I/usr/include/gtk-2.0 -I/usr/lib/x86_64-linux-gnu/gtk-2.0/include -I/usr/include/gio-unix-2.0/ -I/usr/include/cairo -I/usr/include/pango-1.0 -I/usr/include/atk-1.0 -I/usr/include/cairo -I/usr/include/pixman-1 -I/usr/include/libpng12 -I/usr/include/gdk-pixbuf-2.0 -I/usr/include/libpng12 -I/usr/include/pango-1.0 -I/usr/include/harfbuzz -I/usr/include/pango-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include -I/usr/include/freetype2 -D_GNU_SOURCE  -Wall -W
  C++ compiler:                       g++ -std=c++0x
  Enable runtime linker paths:        no
  Enable linker symbol versioning:    no
  Support Microsoft Document Imaging: yes
  Use win32 IO:                       no

 Support for internal codecs:
  CCITT Group 3 & 4 algorithms:       yes
  Macintosh PackBits algorithm:       yes
  LZW algorithm:                      yes
  ThunderScan 4-bit RLE algorithm:    yes
  NeXT 2-bit RLE algorithm:           yes
  LogLuv high dynamic range encoding: yes

 Support for external codecs:
  ZLIB support:                       yes
  Pixar log-format algorithm:         yes
  JPEG support:                       yes
  Old JPEG support:                   yes
  JPEG 8/12 bit dual mode:            no
  ISO JBIG support:                   no
  LZMA2 support:                      no
  ZSTD support:                       no
  WEBP support:                       no

  C++ support:                        yes

  OpenGL support:                     yes


Configured wxWidgets 3.1.3 for `x86_64-pc-linux-gnu'

  Which GUI toolkit should wxWidgets use?                 GTK+ 2 with support for GTK+ printing
  Should wxWidgets be compiled into single library?       yes
  Should wxWidgets be linked as a shared library?         no
  Should wxWidgets support Unicode?                       yes (using wchar_t)
  What level of wxWidgets compatibility should be enabled?
                                       wxWidgets 2.8      no
                                       wxWidgets 3.0      yes
  Which libraries should wxWidgets use?
                                       STL                no
                                       jpeg               sys
                                       png                sys
                                       regex              builtin
                                       tiff               builtin
                                       lzma               no
                                       zlib               sys
                                       expat              sys
                                       libmspack          no
                                       sdl                no
# long build
> make 
...
ar rc /home/duane/sdrplay/wxWidgets-3.1.3/lib/libwx_gtk2u_gl-3.1.a gllib_glcmn.o gllib_gtk_glcanvas.o gllib_glx11.o
ranlib /home/duane/sdrplay/wxWidgets-3.1.3/lib/libwx_gtk2u_gl-3.1.a
...

> make install
...
mkdir -p /home/duane/sdrplay/wxWidgets-staticlib/include/wx-3.1
...
 ------------------------------------------------------
 
 The installation of wxWidgets is finished.  On certain
 platforms (e.g. Linux) you'll now have to run ldconfig
 if you installed a shared library and also modify the
 LD_LIBRARY_PATH (or equivalent) environment variable.
...
> ldconfig

```

## CubicSDR

```
> git clone https://github.com/cjcliffe/CubicSDR.git
> cd CubicSDR
# check WDIR from wxWidgets build
> echo $WDIR
/home/duane/sdrplay/wxWidgets-staticlib
> ls -l $WDIR/bin/wx-config
lrwxrwxrwx 1 root root 77 May 15 22:41 /home/duane/sdrplay/wxWidgets-staticlib/bin/wx-config -> /home/duane/sdrplay/wxWidgets-staticlib/lib/wx/config/gtk2-unicode-static-3.1

> mkdir build; cd build

> cmake ../ -DCMAKE_BUILD_TYPE=Release -DwxWidgets_CONFIG_EXECUTABLE=$WDIR/bin/wx-config
...
-- Build files have been written to: /home/duane/sdrplay/CubicSDR/build

> make
...
[100%] Linking CXX executable x64/CubicSDR
[100%] Built target CubicSDR

# test run
> cd sdrplay/build/x64
> ./CubicSDR
...
...
SDR enumerator starting.
SoapySDR init..
        API Version: v0.8.0
        ABI Version: v0.8
        Install root: /usr/local
        Loading modules... 
        Available factories...null, remote, sdrPlay
  driver = sdrPlay
  label = SDRplay Dev0 RSPdx 200104C742
Make device 0
[INFO] devIdx: 0
[INFO] hwVer: 4
[INFO] mode: 0
  sdrplay_api_api_version=3.070000
  sdrplay_api_hw_version=4
...


# as root 
> make install
...
-- Installing: /usr/local/bin/CubicSDR
...


```
