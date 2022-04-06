cmake_minimum_required (VERSION 3.20.1)

project ("LuaJIT")

if(NOT DEFINED ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
set(LUA_BUILD_DIR ${CMAKE_BINARY_DIR})
else()
set(LUA_BUILD_DIR ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
endif()

if(WIN32)
find_program(POWERSHELL_PATH NAMES powershell)

add_custom_target(luajit_target
	ALL
	COMMAND ${POWERSHELL_PATH} .\\msvcbuild.bat
	WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "luajit.exe" -Destination ${LUA_BUILD_DIR} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "lua51.dll" -Destination ${LUA_BUILD_DIR} -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)

add_custom_command(TARGET luajit_target POST_BUILD
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "jit\*" -Destination ${LUA_BUILD_DIR}/lua -Recurse -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)
endif()

add_library(luajit SHARED IMPORTED GLOBAL)
add_dependencies(luajit luajit_target)

set_target_properties(luajit
    PROPERTIES
    IMPORTED_LOCATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/lua51.dll
    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_LIST_DIR}/src)