---
layout: post
category:
title: "CMake Best Practice"
tags : 
---
{% include JB/setup %}

# Use cmake_minimum_required

~~~cmake
cmake_minimum_required(VERSION 3.3.2)

~~~

cmake_minimum_required calls cmake_policy.
It ensures your CMakeLists.txt will work in future versions of CMake.


# Do not define macro

Macro overrides variables in callers variable scope.
Use function which has a own variable scope.
And, use set(<variable> <value>... PARENT_SCOPE) if you want to set a variables in parent scope.

~~~cmake
function(hello_get_something var_name)
  ...
  set(${var_name} ${ret} PARENT_SCOPE)
endfunction()
~~~

# Use target_sources


~~~cmake
if(WIN32)
  target_sources(hello PRIVATE system_win.cxx)
elseif(UNIX)
  target_sources(hello PRIVATE system_posix.cxx)
else
  target_sources(hello PRIVATE system_generic.cxx)
endif()
~~~

# Use target_compile_features

ToDo: write

# Prefix cache entries

Cache entry is global.
Do not assume your project is the top project.
Use prefix to avoid name conflicts.

~~~cmake
option(${PROJECT_NAME}_USE_FOO "Use Foo" ON)
~~~

# Do not use CMAKE_SOURCE_DIR

Do not assume your project is always the top project.
Use CMAKE_CURRENT_SOURCE_DIR, PROJECT_SOURCE_DIR or <PROJECT-NAME>_SOURCE_DIR instead.

${CMAKE_CURRENT_SOURCE_DIR}

# Do not generate into CMAKE_CURRENT_BINARY_DIR

The directory ${CMAKE_CURRENT_BINARY_DIR} is shared by multple configurations
in multi-configuration build systems such as Visual Studio.
Then, if you run clean in Release configuration, generated files of Debug configuration will also be deleted.
Generated files should be in ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}.

~~~cmake
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/out.c
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/script.py ${CMAKE_CURRENT_SOURCE_DIR}/in.txt > ${CMAKE_CFG_INTDIR}/out.c
  DEPENDS script.py in.txt
  )
~~~

# Use cmake -E

Do not use Unix commands such as cd, rm, mkdir, cp, env and tar.
CMake supports them.
See [cmake(1)](https://cmake.org/cmake/help/latest/manual/cmake.1.html#command-line-tool-mode).

~~~cmake
add_custom_command(
  OUTPUT ...
  COMMAND ${CMAKE_COMMAND} -E ...
  )
~~~

# Do not specify a library type explicitly unless necessary

Users of your library should be able to choose static library or shared library to build.

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
option(hello_BUILD_SHARED_LIBS "build hello as a shared library" ON)
set(BUILD_SHARED_LIBS ${hello_BUILD_SHARED_LIBS})
~~~
Note that directories has a own variable scope,
setting BUILD_SHARED_LIBS does not affect parent directory.

# Define FOLDER directory property

If you have a lot of targets,
FOLDER target property is usefull to organize the project files into folders in IDE.
But, it is tedious to specify the property to all targets.

~~~cmake
define_property(DIRECTORY PROPERTY FOLDER INHERITED BRIEF_DOCS "source file folder name in project file" FULL_DOCS "source file folder name in project file")
~~~
If a target property is not specified, directory property is inherited.

~~~cmake
set_directory_properties(PROPERTIES FOLDER gfx/hello)
~~~

# References

* [CMake - Introduction and best practices](http://www.slideshare.net/DanielPfeifer1/cmake-48475415) by Daniel Pfeifer
* [Modern CMake](https://archive.fosdem.org/2013/schedule/event/moderncmake/) by Alexander Neundorf and Bill Hoffman
