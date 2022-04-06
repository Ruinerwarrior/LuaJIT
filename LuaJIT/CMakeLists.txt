# CMakeList.txt : CMake project for LuaJIT, include source and define
# project specific logic here.
#
#cmake_minimum_required (VERSION 3.20.1)
#
#if(NOT DEFINED ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
#set(LUA_BUILD_DIR ${CMAKE_BINARY_DIR})
#else()
#set(LUA_BUILD_DIR ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
#endif()
#
#message(STATUS "test-${LUA_BUILD_DIR}")
#message(STATUS "test1-${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")
#
#if(WIN32)
#find_program(POWERSHELL_PATH NAMES powershell)
#
#add_custom_target(luajit_target
#	ALL
#	COMMAND ${POWERSHELL_PATH} .\\msvcbuild.bat
#	WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#)
#
#add_custom_command(TARGET luajit_target POST_BUILD
#    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "luajit.exe" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
#    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#    COMMENT "copy .exe"
#)
#
#add_custom_command(TARGET luajit_target POST_BUILD
#    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "luajit.lib" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
#    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#    COMMENT "copy .exe"
#)
#
#add_custom_command(TARGET luajit_target POST_BUILD
#    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "lua51.lib" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
#    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#    COMMENT "copy .exe"
#)
#
#add_custom_command(TARGET luajit_target POST_BUILD
#    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "lua51.dll" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
#    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#    COMMENT "copy .exe"
#)
#
#add_custom_command(TARGET luajit_target POST_BUILD
#    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "jit\*" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/lua -Recurse -Force
#    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
#    COMMENT "copy .exe"
#)
#endif()
#
#add_library(luajit SHARED IMPORTED)
#add_dependencies(luajit luajit_target)
#
#set_target_properties(luajit
#    PROPERTIES
#    OUTPUT_NAME lua51
#    IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/src/lua51.dll
#    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_LIST_DIR}/src)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/bin)

include(ExternalProject)

ExternalProject_Add(project_luajit
  SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/src
  CONFIGURE_COMMAND ""
  INSTALL_COMMAND ""
  BUILD_IN_SOURCE 1
  BUILD_COMMAND .\\msvcbuild.bat
)

add_library(lua51 SHARED IMPORTED)
set_property(TARGET lua51 PROPERTY IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/src/lua51.dll)
add_dependencies(lua51 project_luajit)
target_include_directories(lua51 INTERFACE ${CMAKE_CURRENT_LIST_DIR}/src)

add_library(luajit STATIC IMPORTED)
set_property(TARGET luajit PROPERTY IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/src/luajit.lib)
add_dependencies(luajit project_luajit)
target_include_directories(luajit INTERFACE ${CMAKE_CURRENT_LIST_DIR}/src)