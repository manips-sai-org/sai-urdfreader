cmake_minimum_required(VERSION 3.10)

project(SAI-URDF)
set(PROJECT_VERSION 0.1.0)

option(BUILD_EXAMPLES "Build examples" ON)

set(CMAKE_CXX_FLAGS "-std=c++11 -I/usr/include -I/usr/local/include -fPIC")
if(${CMAKE_SYSTEM_NAME} MATCHES Darwin)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I/opt/homebrew/include")
endif()

# set default build to release with debug info. mimic'ed from RBDL project.
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to 'Release' as none was specified.")
  # Set the default build, and also the possible values of build type for
  # cmake-gui
  set(CMAKE_BUILD_TYPE
      Release
      CACHE STRING "Choose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release"
                                               "MinSizeRel" "RelWithDebInfo")
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
  add_compile_definitions(ENABLE_DEBUG_PRINTS)
endif()

set(SAI-URDF_INCLUDE_DIRS
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_SOURCE_DIR}/src/urdf/urdfdom/urdf_parser/include)

# include urdf, compiled with application
set(URDF_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/src)
set(URDF_INCLUDE_SUB_DIR ${URDF_INCLUDE_DIR}/urdf)
set(URDF_SOURCE
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/pose.cpp
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/link.cpp
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/graphics.cpp
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/world.cpp
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/model.cpp
    ${URDF_INCLUDE_SUB_DIR}/urdfdom/urdf_parser/src/joint.cpp
    ${URDF_INCLUDE_SUB_DIR}/boost_replacement/printf_console.cpp
    ${URDF_INCLUDE_SUB_DIR}/boost_replacement/string_split.cpp)

# Add the include directory to the include paths
include_directories(${URDF_INCLUDE_DIR})

# Create the library
add_library(sai-urdf STATIC ${URDF_SOURCE})
# include tinyxml2
find_library(TINYXML_LIBRARIES tinyxml2)

set(SAI-URDF_LIBRARIES sai-urdf ${TINYXML_LIBRARIES})

#
# export package
#

# export package for use from the build tree
export(TARGETS sai-urdf FILE ${PROJECT_BINARY_DIR}/SAI-URDFTargets.cmake)
export(PACKAGE SAI-URDF)

# configure package
set(CONF_INCLUDE_DIRS ${SAI-URDF_INCLUDE_DIRS} ${TINYXML_INCLUDE_DIR})
set(CONF_LIBRARIES ${SAI-URDF_LIBRARIES})

# create package configuration and version files
configure_file(SAI-URDFConfig.cmake.in
               "${PROJECT_BINARY_DIR}/SAI-URDFConfig.cmake" @ONLY)
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/SAI-URDFConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion)

# add examples
if(BUILD_EXAMPLES)
  add_subdirectory(${PROJECT_SOURCE_DIR}/examples)
endif()
