enable_language(C CXX)

# Use C11 with GNU extensions.
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_EXTENSIONS ON)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Use C++20 with GNU extensions.
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS ON)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Android seems to use various attributes supported by clang but not by
# GCC which causes it to emit lots of warnings. Since these attributes
# don't seem to effect runtime behaviour simply disable the warnings.
add_compile_options(-Wno-attributes)

# silence syntax warns for CXX on Cygwin
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive")

#
# flags for Cygwin
#

# some part of the code does not support Cygwin yet, so we define __linux__ here.
add_compile_definitions(__linux__)

# required ig
add_compile_definitions(_POSIX_TIMERS)

# set this globally for now
# then remove all other defines
add_compile_definitions(_GNU_SOURCE)

# libfsmgr (required by fastboot) requires a 64-bit off_t for lseek. On
# 32-bit glibc platforms this is not the case by default.
add_compile_definitions(_FILE_OFFSET_BITS=64)

# idk whats this but useful to get the build passing
add_compile_definitions(__USE_LINUX_IOCTL_DEFS)

#
# aliases for Cygwin missing symbols
#

add_compile_definitions(off64_t=off_t)
add_compile_definitions(lseek64=lseek)
add_compile_definitions(ftruncate64=ftruncate)
add_compile_definitions(mmap64=mmap)
add_compile_definitions(pread64=pread)
add_compile_definitions(pwrite64=pwrite)
add_compile_definitions(PATH_MAX=__PATH_MAX)

include_directories(/usr/include/linux_compat)

set(android-vendored
	avb
	adb
	base
	core
	extras
	libbase
	libziparchive
	logging
	native
	selinux
	f2fs-tools
	e2fsprogs
	e2fstool
	incremental_delivery
	fmtlib
	boringssl
	mkbootimg
	fec
	squashfs-tools)

# XXX: Consider using https://cmake.org/cmake/help/v3.0/module/ExternalProject.html
if(ANDROID_TOOLS_PATCH_VENDOR AND EXISTS "${ANDROID_PATCH_DIR}/")
	execute_process(COMMAND git submodule --quiet update)
	foreach(v ${android-vendored})
		file(GLOB patches ${ANDROID_PATCH_DIR}/${v}/*.patch)
		if(patches)
			message(STATUS "Applying patches for: ${v}")
			execute_process(COMMAND git -C
				${CMAKE_CURRENT_SOURCE_DIR}/${v} am ${patches}
				RESULT_VARIABLE ret)
			if(NOT "${ret}" STREQUAL "0")
				message(FATAL_ERROR "Couldn't apply patches for ${v}")
			endif()
		endif(patches)
	endforeach(v)
endif()

add_subdirectory(boringssl EXCLUDE_FROM_ALL)
add_subdirectory(fmtlib EXCLUDE_FROM_ALL)

find_package(Protobuf REQUIRED)
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)

include_directories(${PROTOBUF_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(/usr/include/linux_compat/include)


include(CMakeLists.libbase.txt)
include(CMakeLists.libandroidfw.txt)
include(CMakeLists.adb.txt)
include(CMakeLists.sparse.txt)
include(CMakeLists.fastboot.txt)
include(CMakeLists.mke2fs.txt)
include(CMakeLists.make_f2fs.txt)
include(CMakeLists.partition.txt)
include(CMakeLists.mkbootimg.txt)
include(CMakeLists.libufdt.txt)
include(CMakeLists.avb.txt)
include(CMakeLists.zlib.txt)
include(CMakeLists.fec.txt)

# Targets which should be installed by `make install`.
install(TARGETS
	"${ANDROID_MKE2FS_NAME}"
	make_f2fs
	append2simg
	img2simg
	lpadd
	lpdump
	lpflash
	lpmake
	lpunpack
	simg2img
	e2fsdroid
	e2fstool
	ext2simg
	minigzip
	mkbootfs
	fec
	DESTINATION .)

# Install license files.
# Disabled for now, see https://github.com/nmeum/android-tools/issues/30#issuecomment-855365636
#set(LICENSE_DIR "${CMAKE_INSTALL_FULL_DATADIR}/licenses/android-tools")
#install(FILES core/NOTICE RENAME AOSP_LICENSE DESTINATION "${LICENSE_DIR}")
