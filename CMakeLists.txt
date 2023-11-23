cmake_minimum_required(VERSION 3.18)

# Parse metis.h to determine the version 
set(METIS_INCLUDE_FILE ${CMAKE_CURRENT_LIST_DIR}/include/metis.h)
if(EXISTS "${METIS_INCLUDE_FILE}")
  file(STRINGS "${METIS_INCLUDE_FILE}" include_lines)
  foreach(line ${include_lines})
    if(line MATCHES "^#define (METIS_VER_MAJOR|METIS_VER_MINOR|METIS_VER_SUBMINOR)[ \t]*([0-9]+)$")
      set(${CMAKE_MATCH_1} ${CMAKE_MATCH_2})
    endif()
  endforeach()
else()
  set(METIS_VER_MAJOR 5)
  set(METIS_VER_MINOR 2)
  set(METIS_VER_SUBMINOR 1)
endif()

set(METIS_VERSION "${METIS_VER_MAJOR}.${METIS_VER_MINOR}.${METIS_VER_SUBMINOR}")

project(METIS
  VERSION ${METIS_VERSION}
  LANGUAGES C)

include(GNUInstallDirs)

set(SHARED FALSE CACHE BOOL "build a shared library")

if(MSVC)
  set(METIS_INSTALL FALSE)
else()
  set(METIS_INSTALL TRUE)
endif()

# Configure libmetis library.
if(SHARED)
  set(METIS_LIBRARY_TYPE SHARED)
else()
  set(METIS_LIBRARY_TYPE STATIC)
endif(SHARED)

get_filename_component(abs "${CMAKE_CURRENT_LIST_DIR}" ABSOLUTE)
set(METIS_PATH "${abs}")
unset(abs)
set(LIBMETIS_PATH "${METIS_PATH}/libmetis")

include(./conf/gkbuild.cmake)

# METIS' custom options
#option(IDX64 "enable 64 bit ints" OFF)
#option(REAL64 "enable 64 bit floats (i.e., double)" OFF)
#if(IDX64)
#  set(METIS_COPTIONS "${METIS_COPTIONS} -DIDXTYPEWIDTH=64")
#else()
#  set(METIS_COPTIONS "${METIS_COPTIONS} -DIDXTYPEWIDTH=32")
#endif(IDX64)
#if(REAL64)
#  set(METIS_COPTIONS "${METIS_COPTIONS} -DREALTYPEWIDTH=64")
#else()
#  set(METIS_COPTIONS "${METIS_COPTIONS} -DREALTYPEWIDTH=32")
#endif(REAL64)
#
#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${METIS_COPTIONS}")

find_package(GKlib)

# Recursively look for CMakeLists.txt in subdirs.
#add_subdirectory("build/xinclude")
add_subdirectory("libmetis")
add_subdirectory("programs")
