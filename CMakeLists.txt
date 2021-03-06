# Based on https://hsf-training.github.io/hsf-training-cmake-webpage/06-projectstructure/index.html

# Works with 3.14 and tested through 3.18
cmake_minimum_required(VERSION 3.14...3.19)

# Project name and a few useful settings. Other commands can pick up the results
project(
  CMakeSandbox
  VERSION 0.1
  DESCRIPTION "Sandbox for CMake/C++ experiments"
  LANGUAGES CXX)

# Only do these if this is the main project, and not if it is included through
# add_subdirectory
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
  # Let's ensure -std=c++xx instead of -std=g++xx
  set(CMAKE_CXX_EXTENSIONS OFF)

  # Let's nicely support folders in IDE's
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)

  # Testing only available if this is the main app. Note this needs to be done
  # in the main CMakeLists since it calls enable_testing, which must be in the
  # main CMakeLists.
  include(CTest)

  # Docs only available if this is the main app
  find_package(Doxygen)
  if(Doxygen_FOUND)
    add_subdirectory(docs)
  else()
    message(STATUS "Doxygen not found, not building docs")
  endif()
endif()

# FetchContent added in CMake 3.11, downloads during the configure step
# FetchContent_MakeAvailable was not added until CMake 3.14
include(FetchContent)

# Accumulator library This is header only, so could be replaced with git
# submodules or FetchContent
find_package(Boost REQUIRED)
# Adds Boost::boost / Boost::headers (newer FindBoost / BoostConfig 3.15 name)

# Formatting library, adds fmt::fmt
FetchContent_Declare(
  fmtlib
  GIT_REPOSITORY https://github.com/fmtlib/fmt.git
  GIT_TAG 7.0.2
)
FetchContent_MakeAvailable(fmtlib)

# The compiled library code is here
add_subdirectory(src)

# The executable code is here
add_subdirectory(apps)

# Testing only available if this is the main app
if(BUILD_TESTING)
  add_subdirectory(tests)
endif()
