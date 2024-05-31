# OTA Tool - CORN R1 / ALPS R1

A tool to merge incremental OTAs for R1 - **currently available for Linux 64bit only** 

This project is a fork of [payload-dumper-go](https://github.com/ssut/payload-dumper-go).

### Cautions

- Working on a SSD is highly recommended for performance reasons, a HDD could be a bottle-neck.

### Limitations

- Incremental OTA (delta) payload support for brotli_bsdiff + puffin + zucchini - keep anyway in mind that this is a WIP project.

----------------

## Installation

0. Download the latest available binary from [**the releases**]([https://github.com/CORN-R1/ota-tool_r1/releases]) and extract the contents of the downloaded file to a directory on your system.

1. Make sure the extracted binary file has executable permissions. You can use the following command to set the permissions if necessary:
```
chmod +x ota-tool
```
2. Run the following command to add the directory path to your system's PATH environment variable:
```
export PATH=$PATH:/path/to/ota-tool
```
> Note: This command sets the PATH environment variable only for the current terminal session. To make it permanent, you need to add the command to your system's profile file (e.g. .bashrc or .zshrc for Linux/Unix systems).

### Runtime Deps

- libm
- liblzma
- libprotobuf

-----------------

## Usage

Run the following command in your terminal:
```
ota-tool -input /path/to/directory-source-partitions -output /path/to/directory-patched-partitions /path/to/payload.bin
```

Additional options :

- `-concurrency <number-of-workers>` (default = 4)
- `-partitions ` followed by selected partitions comma-separated (e.g. `boot,system,vendor`)

------------------

## Sources

https://android.googlesource.com/platform/system/update_engine/+/master/update_metadata.proto

https://github.com/anonymix007/brotli

https://github.com/anonymix007/bsdiff

https://github.com/anonymix007/bzip2

https://github.com/anonymix007/libchrome

https://github.com/anonymix007/zucchini

https://android.googlesource.com/platform/external/puffin

--------------

## Building

```
A. git submodule update --init --recursive
B. go generate chromeos_update_engine/update_engine.go 
C. go build .
```

### Build Deps

| Dependency     | Debian/Ubuntu package |
|----------------|-----------------------|
| protobuf       | protobuf-compiler     |
| protobuf-devel | protobuf-compiler     |
| glib-devel     | libglib2.0-dev        |
| libevent-devel | libevent-dev          |
| bz2            | libbz2-dev            |
| lzma           | liblzma-dev           |
| abseil         | libabsl-dev           |

### Build Toolchain

- golang
- make
- gcc (or another C compiler)
- g++ (or another C++ compiler)
-----------

### Some "Build hiccups" you may run into (depending on distro/settings)
\
- **Step B error because of File glibconfig.h NOT Found**
```
cp /usr/lib/x86_64-linux-gnu/glib-2.0/include/glibconfig.h /usr/include/glib-2.0/glibconfig.h
```
then repeat step B

\
- **Step C error because of abseil libraries NOT Found and/or abseil namespace mismatch**

**C1.** Generate new Abseil Static Binaries
Clone Abseil Common Libraries (C++) into the project directory.
```
# Change to the directory where you want to create the code repository
$ cd /path/to/project-directory
$ git clone https://github.com/abseil/abseil-cpp.git

$ cd abseil-cpp
$ mkdir build && cd build
$ cmake .. -DCMAKE_INSTALL_PREFIX=~/path/to/project-directory/install

$ cmake --build . --target install
```
Header files will be generated within the `install/include` directory and compiled libraries will be built within the `install/lib` directory.

**C2.** Temporarily remove/uninstall Abseil developer libraries

**C3.** Copy generated headers' directory to /usr/include

**C4.** Rename generated libraries' directory & copy it to /usr/local/lib

**C5.** Edit LDFLAGS in update_engine.go in order to reference the new abseil libraries' location/names

Then run again step C. Once finished you can revert steps C4+C5 & reinstall Abseil developer libraries

----------------

## License

This source code is licensed under the Apache License 2.0 as described in the LICENSE file.
