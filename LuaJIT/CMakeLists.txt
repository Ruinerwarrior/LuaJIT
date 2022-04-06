# CMakeList.txt : CMake project for LuaJIT, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.20.1)

if(NOT DEFINED ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
set(LUA_BUILD_DIR ${CMAKE_BINARY_DIR})
else()
set(LUA_BUILD_DIR ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
endif()

message(STATUS "test-${LUA_BUILD_DIR}")
message(STATUS "test1-${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")

if(WIN32)
find_program(POWERSHELL_PATH NAMES powershell)

add_custom_target(luajit_target
	ALL
	COMMAND ${POWERSHELL_PATH} .\\msvcbuild.bat
	WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "luajit.exe" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "luajit.lib" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "lua51.lib" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "lua51.dll" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "jit\*" -Destination ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/lua -Recurse -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)
endif()

add_library(luajit SHARED IMPORTED)
add_dependencies(luajit luajit_target)

set_target_properties(luajit
    PROPERTIES
    OUTPUT_NAME lua51
    IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/src/lua51.dll
    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_LIST_DIR}/src)