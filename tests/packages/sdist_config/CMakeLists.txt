cmake_minimum_required(VERSION 3.15...3.27)
project(
  sdist_config
  LANGUAGES CXX
  VERSION ${SKBUILD_PROJECT_VERSION})

include(FetchContent)

set(PYBIND11_FINDPYTHON ON)

if(NOT SKBUILD_STATE STREQUAL "sdist"
   AND EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/pybind11/CMakeLists.txt")
  message(STATUS "Using integrated pybind11")
  add_subdirectory(pybind11)
else()
  FetchContent_Declare(
    pybind11
    GIT_REPOSITORY https://github.com/pybind/pybind11.git
    GIT_TAG v2.12.0
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/pybind11)
  FetchContent_MakeAvailable(pybind11)
endif()

pybind11_add_module(sdist_config main.cpp)
install(TARGETS sdist_config DESTINATION .)

# Generation test
include("${CMAKE_CURRENT_BINARY_DIR}/output.cmake")
if(NOT "${MY_VERSION}" STREQUAL "${PROJECT_VERSION}")
  message(FATAL_ERROR "Version mismatch: ${MY_VERSION} != ${PROJECT_VERSION}")
endif()

include("${CMAKE_CURRENT_SOURCE_DIR}/overwrite.cmake")
if(NOT "${MY_NAME}" STREQUAL "${SKBUILD_PROJECT_NAME}")
  message(FATAL_ERROR "Name mismatch: ${MY_NAME} != ${SKBUILD_PROJECT_NAME}")
endif()
