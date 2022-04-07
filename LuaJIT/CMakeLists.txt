# CMakeList.txt : CMake project for LuaJIT, include source and define
# project specific logic here.
#
#cmake_minimum_required (VERSION 3.20.1)

function(add_external_library)
    set(options)
    set(oneValueArgs TARGET WORKING_DIRECTORY OUTPUT_DIRECTORY COMMENT)
    set(multiValueArgs COMMAND INCLUDE_DIRS)
    cmake_parse_arguments(ARGS "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

    if(NOT DEFINED CMAKE_RUNTIME_OUTPUT_DIRECTORY)
        set(OUT ${CMAKE_BINARY_DIR})
    else()
        set(OUT ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
    endif()

    # Specify how to build the result of the library
    add_custom_command(
        OUTPUT "${ARGS_OUTPUT_DIRECTORY}/${ARGS_TARGET}.dll"
        COMMAND ${ARGS_COMMAND}
        WORKING_DIRECTORY "${ARGS_WORKING_DIRECTORY}"
        COMMENT "${ARGS_COMMENT}")

    # Create a target out of the library compilation result
    add_custom_target(${ARGS_TARGET}_target DEPENDS "${ARGS_OUTPUT_DIRECTORY}/${ARGS_TARGET}.dll")

    add_custom_command(
        TARGET ${ARGS_TARGET}_target POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy
                ${ARGS_OUTPUT_DIRECTORY}/${ARGS_TARGET}.dll
                ${OUT}/${ARGS_TARGET}.dll)

    # Create an library target out of the library compilation result
    add_library(${ARGS_TARGET} SHARED IMPORTED GLOBAL)
    add_dependencies(${ARGS_TARGET} ${ARGS_TARGET}_target)

    # Specify where the library is and where to find the headers
    set_target_properties(${ARGS_TARGET}
        PROPERTIES
        IMPORTED_LOCATION "${ARGS_OUTPUT_DIRECTORY}/${ARGS_TARGET}.dll"
        IMPORTED_IMPLIB "${ARGS_OUTPUT_DIRECTORY}/${ARGS_TARGET}.lib"
        INTERFACE_INCLUDE_DIRECTORIES "${ARGS_INCLUDE_DIRS}")
endfunction()

add_external_library(TARGET lua51
    COMMAND           .\\msvcbuild.bat ${BUILD_TYPE}
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/src"
    OUTPUT_DIRECTORY  "${CMAKE_CURRENT_SOURCE_DIR}/src"
    INCLUDE_DIRS      "${CMAKE_CURRENT_SOURCE_DIR}/src"
    COMMENT           "Building YourLib")
