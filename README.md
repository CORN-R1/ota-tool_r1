# OTA Tool

A tool for extracting or patching images from Android OTA delta/incremental payloads.

This project was a originally a fork of [payload-dumper-go](https://github.com/ssut/payload-dumper-go) -

Now it's a **fork of [Emily Sheperd](https://github.com/EmilyShepherd)'s C(++) reimplementation** (_delta/incremental payload support_).

## Features

- Incredibly fast decompression. All decompression progresses are executed in parallel.
- Payload checksum verification.
- Support original zip package that contains payload.bin.
- Incremental OTA (delta) payloads can be applied to old images.

### Cautions

- Working on a SSD is highly recommended for performance reasons, a HDD could be a bottle-neck.

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
Usage: ./ota-tool [options] [inputfile]
  --concurrency int
        Number of multiple workers to extract (default 4)
  --input string
        Set directory for existing partitions
  --list
        Show list of partitions in payload.bin and exit
  --output string
        Set output directory for partitions
  --partitions string
        Only work with selected partitions (comma-separated)
```

Inputfile can either be a `payload.bin` or an uncompressed zip file containing a `payload.bin`.

Command example to patch only system partition :
```
./ota-tool --input /path/to/directory-source-partitions --output /path/to/directory-patched-partitions --partitions system /path/to/payload.bin
```

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
git submodule update --init --recursive
make
```

### Build Deps

| Dependency     | Debian/Ubuntu package |
|----------------|-----------------------|
| protobuf       | protobuf-compiler     |
| protobuf-devel | protobuf-compiler     |
| glib-devel     | libglib2.0-dev        |
| libevent-devel | libevent-dev          |
| libssl-devel   | libssl-dev            |
| bz2            | libbz2-dev            |
| lzma           | liblzma-dev           |
| abseil         | libabsl-dev           |

### Build Toolchain

- make
- cmake
- gcc (or another C compiler)
- g++ (or another C++ compiler)

-----------

### Some "Build hiccups" you may run into (depending on distro/settings)
.
- **Step B error because of File glibconfig.h NOT Found**
```
cp /usr/lib/x86_64-linux-gnu/glib-2.0/include/glibconfig.h /usr/include/glib-2.0/glibconfig.h
```
then repeat step B

.
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

-----------------

`protoc` is not required on the host machine as it will be built as part
of this project. If you want, you can force it to use a host protoc by
specifying PROTOC=protoc or PROTOC=/path/to/protoc when calling make.

```
make PROTOC=protoc
```

NB: This can speed up compilation slightly, however it is not
recommended as protobuf is _very picky_ about protoc versions.

----------------

## License

This source code is licensed under the Apache License 2.0 as described in the LICENSE file.
