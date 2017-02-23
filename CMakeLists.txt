project(AppKit-back)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
	cmake_policy(SET CMP0005 NEW)
endif(COMMAND cmake_policy)

add_definitions(
	-D__APPLE__  -D__MACH__ -D__DYNAMIC__ -D__DARWIN_UNIX03 -DTARGET_OS_MAC=1
	-DGNUSTEP -DGNUSTEP_BASE_LIBRARY
	-D__ENVIRONMENT_MAC_OS_X_VERSION_MIN_REQUIRED__=1080
	-DHAVE_STDINT_H=1 -DHAVE_WCHAR_H=1
	-DHAVE_DISPATCH_DISPATCH_H -DHAVE_BACKTRACE -DHAVE_UNEXPECTED
	-DOBJC_ZEROCOST_EXCEPTIONS=1 -DPRIxPTR=\"lx\" -DPRIuPTR=\"lu\" -DPRIdPTR=\"ld\" -DPRIu64=\"ull\"
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC -w -fconstant-string-class=NSConstantString -fblocks -ggdb -O0")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -nostdlib -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

if(BITS EQUAL 64)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fobjc-runtime=gnustep")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fobjc-runtime=gnustep")
else(BITS EQUAL 64)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fobjc-abi-version=1")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fobjc-abi-version=1")
endif(BITS EQUAL 64)

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/darling")
SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

find_package(X11 REQUIRED)
find_package(Freetype REQUIRED)
find_package(OpenGL REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(PC_CAIRO cairo)
pkg_check_modules(PC_FONTCONFIG fontconfig)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_BINARY_DIR}
	${CMAKE_CURRENT_SOURCE_DIR}/Headers
	${CMAKE_CURRENT_BINARY_DIR}/Headers
	${CMAKE_CURRENT_SOURCE_DIR}/Source
	${CMAKE_CURRENT_SOURCE_DIR}/../gui/Headers
	${CMAKE_CURRENT_BINARY_DIR}/../gui/Headers
	${CMAKE_CURRENT_SOURCE_DIR}/../gui/Headers/Additions
	${CMAKE_CURRENT_BINARY_DIR}/../gui/Headers/Additions
	${CMAKE_CURRENT_SOURCE_DIR}/../../foundation
	${CMAKE_CURRENT_BINARY_DIR}/../../foundation
	${CMAKE_CURRENT_SOURCE_DIR}/../../foundation/Headers
	${CMAKE_CURRENT_BINARY_DIR}/../../foundation/Headers
	${CMAKE_CURRENT_SOURCE_DIR}/../../corefoundation/Headers
	${CMAKE_CURRENT_BINARY_DIR}/../../corefoundation/Headers
	${CMAKE_CURRENT_SOURCE_DIR}/../../libobjc2
	${CMAKE_CURRENT_SOURCE_DIR}/../../libdispatch
	${CMAKE_CURRENT_SOURCE_DIR}/../../icu/icuSources/common
	${CMAKE_CURRENT_SOURCE_DIR}/../../icu/icuSources/i18n
	${DARLING_TOP_DIRECTORY}/platform-include
	${DARLING_TOP_DIRECTORY}/kernel-include
	${DARLING_TOP_DIRECTORY}/compiler-include
	${DARLING_TOP_DIRECTORY}/src/libffi/include
	${DARLING_TOP_DIRECTORY}/src/libc/stdtime/FreeBSD
	${DARLING_TOP_DIRECTORY}/src/libc/gen
	${DARLING_TOP_DIRECTORY}/src/libc/include
	${DARLING_TOP_DIRECTORY}/src/duct/include
	${DARLING_TOP_DIRECTORY}/src/libm/Source
	${DARLING_TOP_DIRECTORY}/src/libmalloc/include/malloc
	${CMAKE_CURRENT_SOURCE_DIR}/../../openssl/src/include
	${CMAKE_CURRENT_SOURCE_DIR}/../../libxml2/include
	${CMAKE_CURRENT_SOURCE_DIR}/../../libxslt/include
	${CMAKE_CURRENT_SOURCE_DIR}/../../zlib

	${X11_INCLUDE_DIRS}
	${FREETYPE_INCLUDE_DIRS}
	${OPENGL_INCLUDE_DIRS}
	${PC_CAIRO_INCLUDE_DIRS}
	${PC_FONTCONFIG_INCLUDE_DIRS}
)

set(OBJC2RUNTIME 1)
set(BASE_NATIVE_OBJC_EXCEPTIONS 1)
set(GS_SINT8 "signed char")
set(GS_UINT8 "unsigned char")
set(GS_SINT16 "signed short")
set(GS_UINT16 "unsigned short")
set(GS_SINT64 "signed long long")
set(GS_UINT64 "unsigned long long")
set(GS_SADDR "signed long")
set(GS_UADDR "unsigned long")
set(GS_FLT32 "float")
set(GS_FLT64 "double")
set(GS_HAVE_I64 1)
set(ac_cv_sizeof_short 2)
set(ac_cv_sizeof_int 4)
set(ac_cv_sizeof_long_long 8)
set(ac_cv_sizeof_float 4)
set(ac_cv_sizeof_double 8)
set(_GSC_S_SHT "_GSC_I16")
set(_GSC_S_INT "_GSC_I32")
set(_GSC_S_LNG "_GSC_I64")
set(_GSC_S_LNG_LNG "_GSC_I64")

if (BITS EQUAL 64)
	set(GS_SINT32 "signed int")
	set(GS_UINT32 "unsigned int")
	set(ac_cv_sizeof_long 8)
	set(ac_cv_sizeof_voidp 8)
	set(GS_SIZEOF_COND_T 48)
	set(GS_SIZEOF_MUTEX_T 64)
	set(GS_ALIGNOF_COND_T 8)
	set(GS_ALIGNOF_MUTEX_T 8)
	set(_GSC_S_LNG "_GSC_I64")
	set(SIZEOF_VOIDP 8)
	set(GS_HAVE_I128 1)
	set(GS_SINT128 "__int128_t")
	set(GS_UINT128 "__uint128_t")
else (BITS EQUAL 64)
	set(GS_SINT32 "signed long")
	set(GS_UINT32 "unsigned long")
	set(ac_cv_sizeof_long 4)
	set(ac_cv_sizeof_voidp 4)
	set(GS_SIZEOF_COND_T 28)
	set(GS_SIZEOF_MUTEX_T 44)
	set(GS_ALIGNOF_COND_T 4)
	set(GS_ALIGNOF_MUTEX_T 4)
	set(_GSC_S_LNG "_GSC_I32")
	set(SIZEOF_VOIDP 4)
	set(GS_SINT128 "struct { gsu8 a[16]; }")
	set(GS_UINT128 "struct { gsu8 a[16]; }")
	set(GS_HAVE_I128 0)
endif (BITS EQUAL 64)

set(HAVE_LIBXML 1)
set(HAVE_BLOCKS 1)
set(HAVE_GNUTLS 0)
set(HAVE_INET_PTON 1)
set(HAVE_INET_NTOP 1)
set(HAVE_LIBDISPATCH 1)
set(HAVE_ICU 1)
set(USE_ZLIB 1)
set(BUGGY_PTR_LIMITS 0)
set(NX_CONST_STRING_CLASS "NSConstantString")
if (BITS EQUAL 64)
	set(GS_NONFRAGILE 1)
else (BITS EQUAL 64)
	set(GS_NONFRAGILE 0)
endif (BITS EQUAL 64)
set(GS_MIXEDABI 0)
set(GS_FAKE_MAIN 0)
set(GS_PASS_ARGUMENTS 0)
set(HAVE_MDNS 0)
set(HAVE_AVAHI 0)
set(GS_HAVE_OBJC_ROOT_CLASS_ATTR 0)
set(GS_WORDS_BIGENDIAN 0)
set(INCLUDE_INTTYPES "#include <stdint.h>")

set(GNUSTEP_GUI_VERSION "0.25.0")
set(GNUSTEP_GUI_MAJOR_VERSION 0)
set(GNUSTEP_GUI_MINOR_VERSION 25)
set(GNUSTEP_GUI_SUBMINOR_VERSION 0)
set(GCC_VERSION "${CMAKE_CXX_COMPILER_VERSION}")

configure_file(config.h.in config.h)

set(appkit_back_SRCS
	Source/GSBackend.m

	Source/gsc/GSContext.m
	Source/gsc/GSGState.m
	Source/gsc/GSStreamContext.m
	Source/gsc/GSStreamGState.m
	Source/gsc/GSFunction.m
	Source/gsc/externs.m

	Source/gsc/gscolors.c

	Source/x11/XGServer.m
	Source/x11/XGServerEvent.m
	Source/x11/XGServerWindow.m
	Source/x11/XGDragView.m
	Source/x11/XIMInputServer.m
	Source/x11/XWindowBuffer
	Source/x11/XGGLFormat.m
	Source/x11/XGGLContext.m

	Source/x11/context.c
	Source/x11/convert.c
	Source/x11/raster.c
	Source/x11/scale.c
	Source/x11/xdnd.c
	Source/x11/xutil.c

	Source/cairo/CairoSurface.m
	Source/cairo/CairoFontInfo.m
	Source/cairo/CairoGState.m
	Source/cairo/CairoContext.m
	Source/cairo/CairoFontEnumerator.m
	Source/cairo/CairoFaceInfo.m
	Source/cairo/CairoPSSurface.m
	Source/cairo/CairoPDFSurface.m
	Source/cairo/../fontconfig/FCFaceInfo.m
	Source/cairo/../fontconfig/FCFontEnumerator.m
	Source/cairo/../fontconfig/FCFontInfo.m
	Source/cairo/XGCairoSurface.m
	Source/cairo/XGCairoXImageSurface.m
	Source/cairo/XGCairoModernSurface.m
)

set_source_files_properties(${appkit_back_SRCS} LANGUAGE C)

add_library(appkit-back OBJECT ${appkit_back_SRCS})

install(DIRECTORY Fonts/Helvetica.nfont	DESTINATION libexec/darling/System/Library/Fonts)
