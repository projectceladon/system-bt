DISCONTINUATION OF PROJECT

This project will no longer be maintained by Intel.

Intel has ceased development and contributions including, but not limited to, maintenance, bug fixes, new releases, or updates, to this project.  

Intel no longer accepts patches to this project.

If you have an ongoing need to use this project, are interested in independently developing it, or would like to maintain patches for the open source software community, please create your own fork of this project.  

Contact: webadmin@linux.intel.com
# Fluoride Bluetooth stack

## Building and running on AOSP
Just build AOSP - Fluoride is there by default.

## Building and running on Linux

Instructions for Ubuntu, tested on 14.04 with Clang 3.5.0 and 16.10 with Clang
 3.8.0

### Download source

```sh
mkdir ~/fluoride
cd ~/fluoride
git clone https://android.googlesource.com/platform/system/bt
```

Install dependencies (require sudo access):

```sh
cd ~/fluoride/bt
build/install_deps.sh
```

Then fetch third party dependencies:

```sh
cd ~/fluoride/bt
mkdir third_party
cd third_party
git clone https://github.com/google/googletest.git
git clone https://android.googlesource.com/platform/external/aac
git clone https://android.googlesource.com/platform/external/libchrome
git clone https://android.googlesource.com/platform/external/libldac
git clone https://android.googlesource.com/platform/external/modp_b64
git clone https://android.googlesource.com/platform/external/tinyxml2
git clone https://android.googlesource.com/platform/hardware/libhardware
```

And third party dependencies of third party dependencies:

```sh
cd fluoride/bt/third_party/libchrome/base/third_party
mkdir valgrind
cd valgrind
curl https://chromium.googlesource.com/chromium/src/base/+/master/third_party/valgrind/valgrind.h?format=TEXT | base64 -d > valgrind.h
curl https://chromium.googlesource.com/chromium/src/base/+/master/third_party/valgrind/memcheck.h?format=TEXT | base64 -d > memcheck.h
```

NOTE: If system/bt is checked out under AOSP, then create symbolic links instead
of downloading sources

```
cd system/bt
mkdir third_party
cd third_party
ln -s ../../../external/aac aac
ln -s ../../../external/libchrome libchrome
ln -s ../../../external/libldac libldac
ln -s ../../../external/modp_b64 modp_b64
ln -s ../../../external/tinyxml2 tinyxml2
ln -s ../../../hardware/libhardware libhardware
ln -s ../../../external/googletest googletest
```

### Generate your build files

```sh
cd ~/fluoride/bt
gn gen out/Default
```

### Build

```sh
cd ~/fluoride/bt
ninja -C out/Default all
```

This will build all targets (the shared library, executables, tests, etc) and
 put them in out/Default. To build an individual target, replace "all" with the
 target of your choice, e.g. ```ninja -C out/Default net_test_osi```.

### Run

```sh
cd ~/fluoride/bt/out/Default
LD_LIBRARY_PATH=./ ./bluetoothtbd -create-ipc-socket=fluoride
```

### Eclipse IDE Support

1. Follows the Chromium project
 [Eclipse Setup Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_eclipse_dev.md)
 until "Optional: Building inside Eclipse" section (don't do that section, we
 will set it up differently)

2. Generate Eclipse settings:

  ```sh
  cd system/bt
  gn gen --ide=eclipse out/Default
  ```

3. In Eclipse, do File->Import->C/C++->C/C++ Project Settings, choose the XML
 location under system/bt/out/Default

4. Right click on the project. Go to Preferences->C/C++ Build->Builder Settings.
 Uncheck "Use default build command", but instead using "ninja -C out/Default"

5. Goto Behaviour tab, change clean command to "-t clean"

