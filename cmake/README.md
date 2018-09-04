[HOME](../README.md)

# Table of Contents
* [Build system](#Build-system)
* [Basic structure](#Basic-structure)
  * [version require and project name](#version-require-and-project-name)
  * [add subdirectory to build](#add-subdirectory-to-build)
  * [add library to build](#add-library-to-build)
  * [add executable](#add-executable)
  * [set output directory](#set-output-directory)
  * [add definitions](#add-definitions)
* [QT property](#QT-property)
  * [qt libray](#qt-libray)
* [special utility](#special-utility)
  * [add_custom_command](#add_custom_command)
    * [target](#target)
	* [output](#output)
  * [add_custom_target](#add_custom_target)
  * [include directories](#include-directories)
  * [link libraries](#link-libraries)
* [Cmake variables](#Cmake-variables)
  * [CMAKE_SOURCE_DIR and PROJECT_SOURCE_DIR](#CMAKE_SOURCE_DIR-and-PROJECT_SOURCE_DIR)
  * [CMAKE_BINARY_DIR, CMAKE_CURRENT_BINARY_DIR, CMAKE_CURRENT_SOURCE_DIR](#CMAKE_BINARY_DIR,-CMAKE_CURRENT_BINARY_DIR,-CMAKE_CURRENT_SOURCE_DIR)
* [function and macro](#function-and-macro)
  
# Build system

* platform: -G 'MSYS Makefiles'
* build type: 
  * -DCMAKE_BUILD_TYPE=Debug
  * SET( CMAKE_BUILD_TYPE Release ... FORCE )

```
$ mkdir Debug
$ cd Debug
$ cmake -G "MSYS Makefiles" -DCMAKE_BUILD_TYPE=Debug
```

# Basic structure
## version require and project name
```
cmake_minimum_required(VERSION 3.2)
project(testPilot)
```
## add subdirectory to build
```
add_subdirectory(app)
```
## add library to build
```
add_library(quickwidgetproxy SHARED quickwidgetproxy.cpp)
```

## add executable 
```
add_executable(test_serial testserial.cpp)
target_link_libraries (test_serial utils serial)
```
## set output directory
* global
```
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
```
* special target
```
set_target_properties( targets...
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
)
```

## add definitions
```
add_definitions(-DDEFAULT_CONFIG_FILENAME="default.xml")
```

# QT property
```
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
```
## qt libray
```
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)
qt5_use_modules(test_serial Core Widgets)
```

# special utility
## add_compile_options(-fopenmp)
This option will only influence the compile object file, and if you use
```
set(GCC_COVERAGE_COMPILE_FLAGS "-fopenmp")
set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS}" )
```
It will also affect in link process.

## add_custom_command
### target
```
add_custom_command(TARGET MyTarget PRE_BUILD
                   COMMAND ${CMAKE_COMMAND} -E copy_directory
                   ${CMAKE_SOURCE_DIR}/config $<TARGET_FILE_DIR:MyTarget>)
```
### output
```
add_custom_command(
  OUTPUT  ${UAVObjectsInit}
  COMMAND  ${CMAKE_BINARY_DIR}/bin/uavobjgenerator -gcs ${CMAKE_SOURCE_DIR}/xml/uavobjectdefinition ${CMAKE_CURRENT_SOURCE_DIR})set(LIBFOO_TAR_HEADERS
  "${CMAKE_CURRENT_BINARY_DIR}/include/foo/foo.h"
  "${CMAKE_CURRENT_BINARY_DIR}/include/foo/foo_utils.h"
)

add_custom_command(OUTPUT ${LIBFOO_TAR_HEADERS}
  COMMAND ${CMAKE_COMMAND} -E tar xzf "${CMAKE_CURRENT_SOURCE_DIR}/libfoo/foo.tar"
  COMMAND ${CMAKE_COMMAND} -E touch ${LIBFOO_TAR_HEADERS}
  WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/include/foo"
  DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/libfoo/foo.tar"
  COMMENT "Unpacking foo.tar"
  VERBATIM
)

add_custom_target(libfoo_untar DEPENDS ${LIBFOO_TAR_HEADERS})

add_library(foo INTERFACE)
target_include_directories(foo INTERFACE "${CMAKE_CURRENT_BINARY_DIR}/include/foo")
target_link_libraries(foo INTERFACE ${FOO_LIBRARIES})

```
## add_custom_target
```
add_custom_target(
  CopyShare ALL
  COMMAND
  ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/share ${PROJECT_BINARY_DIR}/share
  )
```

## include directories
```
include_directories(${PROJECT_SOURCE_DIR}/mainwindow)
```

## link libraries
for general library in project, just add the library target to target_link_libraries, for external library use the following way to get full library and add to target_link_libraries, don't use link_directoris
```
find_library(PYTHON_LIBRARY python3 HINTS /usr/lib/x86_64-linux-gnu)
target_link_libraries(test aaa PUBLIC ${PYTHON_LIBRARY})
```

# Cmake variables
## CMAKE_SOURCE_DIR and PROJECT_SOURCE_DIR
* CMAKE_SOURCE_DIR : the source directory of cmake
* PROJECT_SOURCE_DIR : current project source directory

## CMAKE_BINARY_DIR, CMAKE_CURRENT_BINARY_DIR, CMAKE_CURRENT_SOURCE_DIR
* CMAKE_BINARY_DIR : the binary directory of cmake
* CMAKE_CURRENT_BINARY_DIR : the current binary directory of cmake
* CMAKE_CURRENT_SOURCE_DIR : the current source directory of cmake

# function and macro
use the following to include macro or function
```
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")
include(testPilotFunctions)
```
test code
```
set(var "ABC")

macro(Moo arg)
  message("arg = ${arg}")
  set(arg "abc")
  message("# After change the value of arg.")
  message("arg = ${arg}")
endmacro()
message("=== Call macro ===")
Moo(${var})

function(Foo arg)
  message("arg = ${arg}")
  set(arg "abc")
  message("# After change the value of arg.")
  message("arg = ${arg}")
endfunction()
message("=== Call function ===")
Foo(${var})
```
output
```
=== Call macro ===
arg = ABC
# After change the value of arg.
arg = ABC
=== Call function ===
arg = ABC
# After change the value of arg.
arg = abc
```

conclution: They are string replacements much like the C preprocessor would do with a macro. If you want true CMake variables and/or better CMake scope control you should look at the function command.
