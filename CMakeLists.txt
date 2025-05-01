cmake_minimum_required(VERSION 3.25)

project(Lua
        LANGUAGES C
        VERSION 5.1.5
        DESCRIPTION "Lua Programming Language")

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE
        Debug
        CACHE STRING "Release"
        FORCE)
endif()

file(GLOB_RECURSE
     SRC_FILES
     CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c"
)

file(GLOB_RECURSE
     HEADER_FILES
     CONFIGURE_DEPENDS
     "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
)

message(STATUS "Source Files: ${SRC_FILES}")

message(STATUS "Header Files: ${HEADER_FILES}")

set(EXECUTABLES_MAIN_FILES "")

set(LIBRARY_SOURCE_FILES "")

foreach(file ${SRC_FILES})

    file(READ "${file}" FILE_CONTENTS)

    string(FIND "${FILE_CONTENTS}" "int main" HAS_MAIN)

    if (NOT HAS_MAIN EQUAL -1)
        list(APPEND EXECUTABLES_MAIN_FILES "${file}")
    else()
        list(APPEND LIBRARY_SOURCE_FILES "${file}")
    endif()

endforeach()

message(STATUS "Compiling static library 'lua_static'")
add_library(lua_static STATIC ${LIBRARY_SOURCE_FILES} ${HEADER_FILES})
target_compile_features(lua_static PRIVATE c_std_99)

message(STATUS "Compiling shared library 'lua_shared'")
add_library(lua_shared SHARED ${LIBRARY_SOURCE_FILES} ${HEADER_FILES})
set_target_properties(lua_shared PROPERTIES VERSION 1.0 SOVERSION 1)
target_compile_features(lua_shared PRIVATE c_std_99)

set(exec_id 0)

foreach(main_file ${EXECUTABLES_MAIN_FILES})

    math(EXPR exec_id "${exec_id} + 1")

    get_filename_component(exec_name "${main_file}" NAME_WE)

    set(target_name "${exec_name}")

    add_executable(${target_name}
                   ${main_file}
                   ${LIBRARY_SOURCE_FILES}
                   ${HEADER_FILES})

    target_compile_features(${target_name}
                            PRIVATE
                            c_std_99)

    target_compile_options(${target_name}
                           PRIVATE
                           "$<$<CONFIG:Debug>:${DEBUG_FLAGS}>")

    message(STATUS "Compiling executable '${target_name}' with main: ${main_file}")

endforeach()
