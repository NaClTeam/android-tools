# DEPRECATION NOTICE: DEVELOPMENT MOVED TO https://github.com/ookiineko/android-image-tools

# android-tools for Cygwin

Git repository to make it easier to package certain command line
utilities provided by [android-tools][android-tools].

# Status

Currently the following tools are supported:

* adb
* fastboot
* dmtracedump, etc1tool, hprof-conv, sqlite3
* scrcpy
* mke2fs, make_f2fs, e2fsdroid (required by fastboot)
* [e2fstool][e2fstool]
* simg2img, img2simg, append2simg, ext2simg
* lpdump, lpmake, lpadd, lpflash, lpunpack
* mkbootimg, unpack_bootimg, repack_bootimg, avbtool
* mkdtboimg
* minigzip, mkbootfs, fec
* busybox, openssl, lz4
* cygpath

# Dependencies

On Cygwin please install [these headers][linux_compat] to /usr/include.

The following libraries are required by android-tools:

1. [PCRE][PCRE]
2. [Google Test][gtest]
3. [protobuf][protobuf]
4. [brotli][brotli]
5. [zstd][zstd]
6. [lz4][lz4]
7. [zlib][zlib]

Python 3 is optionally needed as a run-time dependency in order to use
the `mkbootimg`, `unpack_bootimg`, and `repack_bootimg` scripts which
are all written in Python.

Additionally the following software is required at compile-time:

1. A C and C++ compiler (either [GCC][gcc] >= 10.X or [clang][clang])
2. The [Go compiler][golang]
3. [CMake][cmake]
4. [Perl][perl]

*Currently the build system doesn't check whether all of these are installed.*

# Installation

Source tarballs containing an already patched version of all vendored
dependencies are available on the [GitHub Release Page][release-page].

These tarballs should be used for packaging and general installation.
After the tarball was downloaded and extracted android-tools can be
build and installed as follows:

````shell
$ cmake -B build -G Ninja \
        -DCMAKE_INSTALL_PREFIX=/ \
        -DCMAKE_BUILD_TYPE=Release
$ cmake --build build
$ DESTDIR=install cmake --install build
````

# Generating tarballs

New source tarballs can be created from the Git repository using:

````shell
$ cmake -B build -G Ninja \
        -DCMAKE_INSTALL_PREFIX=/ \
        -DCMAKE_BUILD_TYPE=Release
$ ninja -C build package_source
````

Before a new release is uploaded a new `git-tag(1)` should be created
for the release. Afterwards the tarball can be uploaded to the [GitHub
Release Page][release-page].

# See also

The original Alpine Linux [android-tools repo][upstream-repo] by [nmeum][original-author]
which this project bases on.

The Arch Linux [android-tools package][arch-linux] by [Anatol Pomozov][anatol.pomozov]
which inspired this project. Most definitions in the `CMakeLists.txt`
have been copied from Anatol's ruby script.

[upstream-repo]: https://github.com/nmeum/android-tools
[original-author]: https://github.com/nmeum
[android-tools]: https://sites.google.com/a/android.com/tools/
[arch-linux]: https://www.archlinux.org/packages/community/x86_64/android-tools/
[release-page]: https://github.com/ookiineko/android-tools-cygwin/releases
[PCRE]: http://pcre.sourceforge.net/
[gtest]: https://github.com/google/googletest
[gcc]: https://gcc.gnu.org/
[clang]: https://llvm.org/
[golang]: https://golang.org/
[cmake]: https://cmake.org/
[perl]: https://www.perl.org/
[protobuf]: https://github.com/protocolbuffers/protobuf
[brotli]: https://github.com/google/brotli
[zstd]: https://facebook.github.io/zstd/
[lz4]: https://github.com/lz4/lz4
[anatol.pomozov]: https://github.com/anatol
[zlib]: https://zlib.net
[e2fstool]: https://github.com/svoboda18/e2fstool
[linux_compat]: https://github.com/ookiineko/linux_compat
