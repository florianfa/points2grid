# setup the project
# =================

cmake_minimum_required(VERSION 2.8)
project(points2grid CXX)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")

# set build type
# ==============

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE RelWithDebInfo CACHE STRING 
        "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel" FORCE)
endif()
message(STATUS "Setting ${CMAKE_PROJECT_NAME} build type - ${CMAKE_BUILD_TYPE}")

# setup dependencies
# ==================

# Boost (required) 
# ----------------

set(Boost_USE_STATIC_LIBS        ON)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME    OFF)
find_package( Boost 1.44 COMPONENTS filesystem program_options system REQUIRED )

if(Boost_FOUND AND Boost_PROGRAM_OPTIONS_FOUND)
   include_directories(${Boost_INCLUDE_DIRS})
endif()

# make these available for the user to set.
mark_as_advanced(CLEAR Boost_INCLUDE_DIR) 
mark_as_advanced(CLEAR Boost_LIBRARY_DIRS) 
link_directories(${Boost_LIBRARY_DIRS}) 

# cURL (optional)
# ---------------

find_package(curl)
include_directories(${CURL_INCLUDE_DIRS})

# libLAS
# ------

find_package(LibLAS REQUIRED)
include_directories(${LIBLAS_INCLUDE_DIR})

# generate our configuration header
# =================================

configure_file(${PROJECT_SOURCE_DIR}/config.h.in
  ${PROJECT_SOURCE_DIR}/config.h)

# and now the actual project that we'll build
# ===========================================

set(POINTS2GRID_CPP
  GridFile.cpp
  GridMap.cpp
  InCoreInterp.cpp
  Interpolation.cpp
  main.cpp
  OutCoreInterp.cpp
)

set(POINTS2GRID_HPP
  config.h
  CoreInterp.h
  Global.h
  GridFile.h
  GridMap.h
  GridPoint.h
  InCoreInterp.h
  Interpolation.h
  OutCoreInterp.h
)

source_group("CMake Files" FILES CMakeLists.txt)
source_group("Header Files" FILES ${POINTS2GRID_HPP})
source_group("Source Files" FILES ${POINTS2GRID_CPP})

set_source_files_properties(${POINTS2GRID_HPP} PROPERTIES LANGUAGE CXX)

add_executable(points2grid ${POINTS2GRID_HPP} ${POINTS2GRID_CPP})
target_link_libraries(points2grid ${CURL_LIBRARIES} ${LIBLAS_LIBRARIES})