project(SPro)
cmake_minimum_required(VERSION 2.8)
#add_subdirectory(foo)

# autoconf simulation
include(CheckIncludeFiles)
include(CheckFunctionExists)
include(CheckTypeSize)

CHECK_INCLUDE_FILES("stdlib.h" HAVE_STDLIB_H)
CHECK_INCLUDE_FILES("stdarg.h" HAVE_STDARG_H)
CHECK_INCLUDE_FILES("stddef.h" HAVE_STDDEF_H)
CHECK_INCLUDE_FILES("stdlib.h;stddef.h;stdarg.h" STDC_HEADERS)
CHECK_INCLUDE_FILES("stdint.h" HAVE_STDINT_H)
CHECK_INCLUDE_FILES("malloc.h" HAVE_MALLOC_H)
CHECK_INCLUDE_FILES("unistd.h" HAVE_UNISTD_H)
CHECK_INCLUDE_FILES("string.h" HAVE_STRING_H)
CHECK_INCLUDE_FILES("memory.h" HAVE_MEMORY_H)
CHECK_INCLUDE_FILES("math.h" HAVE_MATH_H)
CHECK_INCLUDE_FILES("limits.h" HAVE_LIMITS_H)
CHECK_INCLUDE_FILES("sys/types.h" HAVE_SYS_TYPES_H)

CHECK_FUNCTION_EXISTS(strcasecmp HAVE_STRCASECMP)

option(SPHERE "Build with SPHERE support" OFF)
option(BUILD_TOOLS "Build tools" ON)

set(VERSION "4.0.0")

include(TestBigEndian)
TEST_BIG_ENDIAN(WORDS_BIGENDIAN)

include(CheckTypeSize)
CHECK_TYPE_SIZE(short SIZEOF_SHORT)
CHECK_TYPE_SIZE(long SIZEOF_LONG)

CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/config.h.template ${CMAKE_CURRENT_BINARY_DIR}/config.h)

# MSVC specifics
if(MSVC)
    ADD_DEFINITIONS(-D_USE_MATH_DEFINES)
    ADD_DEFINITIONS(-Dstrcasecmp=_stricmp)
endif()

# build library
ADD_DEFINITIONS(-DHAVE_CONFIG_H)
INCLUDE_DIRECTORIES(SYSTEM ${CMAKE_CURRENT_SOURCE_DIR})
INCLUDE_DIRECTORIES(SYSTEM ${CMAKE_CURRENT_BINARY_DIR})

set(LIB_SOURCE_FILES system.h spro.h sig.c spf.c header.c misc.c lpc.c convert.c fft.c)
add_library(spro STATIC ${LIB_SOURCE_FILES})

# build tools
if(BUILD_TOOLS)

set(DEP_LIBS spro)

add_executable(scopy scopy.c getopt.c getopt1.c)
target_link_libraries(scopy ${DEP_LIBS})

add_executable(scompare scompare.c getopt.c getopt1.c)
target_link_libraries(scompare ${DEP_LIBS})

add_executable(slpc slpc.c getopt.c getopt1.c)
target_link_libraries(slpc ${DEP_LIBS})

add_executable(slpcep slpcep.c getopt.c getopt1.c)
target_link_libraries(slpcep ${DEP_LIBS})

add_executable(sfbank sfbank.c getopt.c getopt1.c)
target_link_libraries(sfbank ${DEP_LIBS})

add_executable(sfbcep sfbcep.c getopt.c getopt1.c)
target_link_libraries(sfbcep ${DEP_LIBS})

endif()