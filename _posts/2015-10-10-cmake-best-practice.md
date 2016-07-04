---
layout: post
category:
title: "CMake Best Practice"
tags : cmake
---

## Use function instread of macro

Macro overrides variables in callers variable scope.
Use function which has a own variable scope.
And, use `set(<variable> <value>... PARENT_SCOPE)` if you want to set a variables in parent scope.

~~~cmake
function(hello_get_something var_name)
  ...
  set(${var_name} ${ret} PARENT_SCOPE)
endfunction()
~~~

## Use target_sources

Using `target_sources` makes more concise than using custom variable.

~~~cmake
set(src hello.cxx)

if(WIN32)
  list(APPEND src system_win.cxx)
elseif(UNIX)
  list(APPEND src system_posix.cxx)
else()
  list(APPEND src system_generic.cxx)
endif()

add_library(hello ${src})
~~~

~~~cmake
add_library(hello hello.cxx)

if(WIN32)
  target_sources(hello PRIVATE system_win.cxx)
elseif(UNIX)
  target_sources(hello PRIVATE system_posix.cxx)
else()
  target_sources(hello PRIVATE system_generic.cxx)
endif()
~~~

## Use target_compile_features

Do not use a compile option `-std=c++11` explicitly.

~~~cmake
target_compile_options(hello PRIVATE -std=c++11)
~~~

~~~cmake
target_compile_features(hello PRIVATE cxx_variadic_templates)
~~~

## Prefix cache entries

Cache entry is global.
Do not assume your project is the top project.
Use prefix to avoid name conflicts.

~~~cmake
option(hello_use_foo "Use Foo" ON)
~~~

## Do not use CMAKE_SOURCE_DIR

Do not assume your project is always the top project.
Use `CMAKE_CURRENT_SOURCE_DIR`, `PROJECT_SOURCE_DIR` or `<PROJECT-NAME>_SOURCE_DIR` instead.

## Do not generate into CMAKE_CURRENT_BINARY_DIR

The directory `${CMAKE_CURRENT_BINARY_DIR}` is shared by multple configurations
in multi-configuration build systems such as Visual Studio.
Then, if you run clean in Release configuration, generated files of Debug configuration will also be deleted.
Generated files should be in `${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}`.

~~~cmake
target_sources(hello PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/out.c)
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/out.c
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/script.py ${CMAKE_CURRENT_SOURCE_DIR}/in.txt > ${CMAKE_CFG_INTDIR}/out.c
  DEPENDS script.py in.txt
  )
~~~
On the other hand, you don't need to use `CMAKE_CFG_INTDIR` in the following variables
because multi-configuration build systems automatically append `/${CMAKE_CFG_INTDIR}` if `CMAKE_CFG_INTDIR` is not included.

* CMAKE_ARCHIVE_OUTPUT_DIRECTORY
* CMAKE_LIBRARY_OUTPUT_DIRECTORY
* CMAKE_RUNTIME_OUTPUT_DIRECTORY

If you write the following script, the output directory is `${CMAKE_BINARY_DIR}/bin/${CMAKE_CFG_INTDIR}` in multi-configuration build systems.

~~~cmake
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
~~~

## Use cmake -E

Do not use Unix commands such as cd, rm, mkdir, cp, env and tar.
CMake supports them.
See [cmake(1)](https://cmake.org/cmake/help/latest/manual/cmake.1.html#command-line-tool-mode).

~~~cmake
add_custom_command(
  OUTPUT ...
  COMMAND ${CMAKE_COMMAND} -E ...
  )
~~~

## Do not specify a library type explicitly unless necessary

Users of your library should be able to choose static library or shared library to build.
Do not specify a library type explicitly unless necessary.

~~~cmake
add_library(hello hello.cxx)
~~~
Variable BUILD_SHARED_LIBS controls it.

~~~
cmake -D BUILD_SHARED_LIBS=ON $src_dir
~~~

The initial value of BUILD_SHARED_LIBS is OFF.
If you want to change the defalut, make it a option.

~~~cmake
option(BUILD_SHARED_LIBS "build as shared libraries" ON)
~~~
This violates "Prefix cache entries" rule.
Another option is:

~~~cmake
option(hello_build_shared_libs "build hello as a shared library" ON)
set(BUILD_SHARED_LIBS ${hello_build_shared_libs})
~~~
Note that directories has a own variable scope,
setting BUILD_SHARED_LIBS does not affect the parent directory.

## Define FOLDER directory property

If you have a lot of targets,
FOLDER target property is usefull to organize the project files into folders in IDE.
But, it is tedious to specify the property to all targets.
There is a solution to specify a folder name to a directory.
If a target property is not specified, directory property is inherited.
First, define property FOLDER as INHERITED.

~~~cmake
define_property(DIRECTORY PROPERTY FOLDER INHERITED BRIEF_DOCS "source file folder name in project file" FULL_DOCS "source file folder name in project file")
~~~

Then, set FOLDER directory property to the current directory.

~~~cmake
set_directory_properties(PROPERTIES FOLDER gfx/hello)
~~~

## References

* [CMake Documentation](https://cmake.org/documentation/)
* [CMake - Introduction and best practices](http://www.slideshare.net/DanielPfeifer1/cmake-48475415) by Daniel Pfeifer
* [Modern CMake](https://archive.fosdem.org/2013/schedule/event/moderncmake/) by Alexander Neundorf and Bill Hoffman
