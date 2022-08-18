# android-tools for Cygwin

Git repository to make it easier to package certain command line
utilities provided by [android-tools][android-tools].

# Status

Currently the following tools are supported:

* mke2fs.android, make_f2fs
* simg2img, img2simg, append2simg
* lpdump, lpmake, lpadd, lpflash, lpunpack
* mkbootimg, unpack_bootimg, repack_bootimg

# Dependencies

The following libraries are required by android-tools:

1. [PCRE][PCRE]
2. [Google Test][gtest]
3. [protobuf][protobuf]
4. [brotli][brotli]
5. [zstd][zstd]
6. [lz4][lz4]

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
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_LEGACY_CYGWIN_WIN32=1
$ cmake --build build
$ cmake --install build
````

# Generating tarballs

New source tarballs can be created from the Git repository using:

````shell
$ cmake -B build -G Ninja \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_LEGACY_CYGWIN_WIN32=1
$ ninja -C build package_source
````

Before a new release is uploaded a new `git-tag(1)` should be created
for the release. Afterwards the tarball can be uploaded to the [GitHub
Release Page][release-page].

# See also

The Arch Linux [android-tools package][arch-linux] by [Anatol Pomozov][anatol.pomozov]
which inspired this project. Most definitions in the `CMakeLists.txt`
have been copied from Anatol's ruby script.

[android-tools]: https://sites.google.com/a/android.com/tools/
[void-linux]: https://github.com/void-linux/void-packages/tree/master/srcpkgs/android-tools
[arch-linux]: https://www.archlinux.org/packages/community/x86_64/android-tools/
[alpine-linux]: https://pkgs.alpinelinux.org/package/edge/testing/x86_64/android-tools
[release-page]: https://github.com/nmeum/android-tools/releases
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
