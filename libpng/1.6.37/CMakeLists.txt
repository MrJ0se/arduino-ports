
set(ZLIB_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/../zlib/1.2.12" "${CMAKE_CURRENT_LIST_DIR}/../zlib/1.2.12/b")
set(ZLIB_LIBRARY "${CMAKE_CURRENT_LIST_DIR}/../zlib/1.2.12/b/libzlibstatic.a")

cmake_minimum_required(VERSION 3.1)
cmake_policy(VERSION 3.1)

project(libpng C)

set(PNGLIB_MAJOR 1)
set(PNGLIB_MINOR 6)
set(PNGLIB_RELEASE 37)
set(PNGLIB_NAME libpng${PNGLIB_MAJOR}${PNGLIB_MINOR})
set(PNGLIB_VERSION ${PNGLIB_MAJOR}.${PNGLIB_MINOR}.${PNGLIB_RELEASE})

find_library(M_LIBRARY m)
if(NOT M_LIBRARY)
  set(M_LIBRARY "")
endif()

set(PNG_PREFIX "" CACHE STRING "Prefix to add to the API function names")
set(DFA_XTRA "" CACHE FILEPATH "File containing extra configuration settings")

# SET LIBNAME
set(PNG_LIB_NAME png${PNGLIB_MAJOR}${PNGLIB_MINOR})

include(CheckCSourceCompiles)

include_directories(${CMAKE_CURRENT_BINARY_DIR} ${ZLIB_INCLUDE_DIR})

# No awk available to generate sources; use pre-built pnglibconf.h
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/scripts/pnglibconf.h.prebuilt
               ${CMAKE_CURRENT_BINARY_DIR}/pnglibconf.h)
add_custom_target(genfiles) # Dummy

# OUR SOURCES
set(libpng_public_hdrs
  png.h
  pngconf.h
  "${CMAKE_CURRENT_BINARY_DIR}/pnglibconf.h"
)
set(libpng_private_hdrs
  pngpriv.h
  pngdebug.h
  pnginfo.h
  pngstruct.h
)
set(libpng_sources
  ${libpng_public_hdrs}
  ${libpng_private_hdrs}
  png.c
  pngerror.c
  pngget.c
  pngmem.c
  pngpread.c
  pngread.c
  pngrio.c
  pngrtran.c
  pngrutil.c
  pngset.c
  pngtrans.c
  pngwio.c
  pngwrite.c
  pngwtran.c
  pngwutil.c
  ${libpng_arm_sources}
  ${libpng_intel_sources}
  ${libpng_mips_sources}
  ${libpng_powerpc_sources}
)

include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${ZLIB_INCLUDE_DIR})

unset(PNG_LIB_TARGETS)

set(PNG_LIB_NAME_STATIC png_static)
add_library(png_static STATIC ${libpng_sources})
add_dependencies(png_static genfiles)

set_target_properties(png_static PROPERTIES
  OUTPUT_NAME "${PNG_LIB_NAME}"
  CLEAN_DIRECT_OUTPUT 1)
list(APPEND PNG_LIB_TARGETS png_static)
target_link_libraries(png_static ${ZLIB_LIBRARY} ${M_LIBRARY})

include(CMakeParseArguments)

# Create source generation scripts.
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/scripts/genchk.cmake.in
               ${CMAKE_CURRENT_BINARY_DIR}/scripts/genchk.cmake @ONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/scripts/genout.cmake.in
               ${CMAKE_CURRENT_BINARY_DIR}/scripts/genout.cmake @ONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/scripts/gensrc.cmake.in
               ${CMAKE_CURRENT_BINARY_DIR}/scripts/gensrc.cmake @ONLY)