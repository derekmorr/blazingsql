﻿#=============================================================================
# Copyright 2018 BlazingDB, Inc.
#     Copyright 2018 Percy Camilo Triveño Aucahuasi <percy@blazingdb.com>
#=============================================================================

#pass the dependency libraries as optional arguments using ${ARGN}
#NOTE the order of libraries matter, so try to link first with the most high level lib
function(configure_test TEST_NAME Tests_SRCS)
    include_directories(
        ${CMAKE_SOURCE_DIR}/src
        ${CMAKE_SOURCE_DIR}/tests/utils
        ${CMAKE_SOURCE_DIR}/thirdparty/jitify

        ${CMAKE_SOURCE_DIR}/src/gdf_wrapper
        $ENV{PREFIX}/include
    )

    add_executable(${TEST_NAME} ${Tests_SRCS} ${CMAKE_SOURCE_DIR}/tests/cython_errors_dummy.cpp)
    link_directories($ENV{PREFIX}/lib)
    # TODO percy do not use libgdf.so here and use gdf (the static lib) instead
    target_link_libraries(${TEST_NAME}
        gmock_main
        gmock
        gtest
        arrow
        blazingdb-io
        blazingdb-transport
        blazingsql-engine
        ${PYTHON_LIBRARIES}
    )

    set_target_properties(${TEST_NAME} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/gtests/")

    #register the target as CMake test so we can use ctest for this executable
    add_test(NAME ${TEST_NAME} COMMAND ${TEST_NAME})
endfunction()

## Main ##

enable_testing()

# Configure unit tests with Google Test
# -------------------
message(STATUS "******** Configuring tests ********")


add_subdirectory(logical-filter)
add_subdirectory(calcite-interpreter)
add_subdirectory(cudf/generator)
add_subdirectory(nvcategory)
add_subdirectory(integration)
add_subdirectory(dtypes)
add_subdirectory(pjoins)
add_subdirectory(evaluate_query)
add_subdirectory(order-by)
add_subdirectory(gdf_column_cpp)
add_subdirectory(where)
add_subdirectory(joins)
add_subdirectory(tpch)
add_subdirectory(io-test)
add_subdirectory(union)
add_subdirectory(unary)
add_subdirectory(groupbywoagg)

# TODO Felipe JP
#add_subdirectory(interpreter)

add_subdirectory(utils)
add_subdirectory(resultset-repository)
add_subdirectory(parser)
add_subdirectory(transport)

message(STATUS "******** Tests are ready ********")