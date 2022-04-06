cmake_minimum_required (VERSION 3.20.1)

project ("LuaJIT")

set(LUA_BUILD_DIR ${PROJECT_BINARY_DIR})

if(DEFINED ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
set(LUA_BUILD_DIR ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
endif()

message(STATUS "test1 ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}")
message(STATUS "test2 ${LUA_BUILD_DIR}")

SET(LIB_FILE ${LUA_BUILD_DIR}/lua51.dll)

if(WIN32)
find_program(POWERSHELL_PATH NAMES powershell)

#add_custom_command(OUTPUT ${LIB_FILE} ${CMAKE_CURRENT_LIST_DIR}/src/lua51.dll
#                   COMMAND ${POWERSHELL_PATH} .\\msvcbuild.bat 
#                   WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src)

add_custom_target(luajit_target
	ALL
	COMMAND ${POWERSHELL_PATH} .\\msvcbuild.bat
	WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
)

#add_custom_target(luajit_target DEPENDS ${LIB_FILE})

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
    COMMAND ${POWERSHELL_PATH} Copy-Item -Path "jit\*" -Destination ${LUA_BUILD_DIR}/lua/jit -Recurse -Force
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/src
    COMMENT "copy .exe"
)
endif()

add_library(luajit STATIC IMPORTED GLOBAL)
add_dependencies(luajit luajit_target)

set_target_properties(luajit
    PROPERTIES
    IMPORTED_LOCATION ${LUA_BUILD_DIR}/lua51.dll
    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_LIST_DIR}/src)