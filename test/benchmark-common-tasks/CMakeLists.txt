message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")

message(STATUS "REALM_WOLFSSL_LIB wolfssl: ${REALM_WOLFSSL_LIB}")

add_executable(realm-benchmark-common-tasks main.cpp)

if(REALM_HAVE_WOLFSSL)
	message(STATUS "benchmark common tasks cmake: ")
	# set in parent cmake:
	message(STATUS "Found REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
	message(STATUS "Found REALM_WOLFSSL_LIB=${REALM_WOLFSSL_LIB}")
else()
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

target_link_libraries(realm-benchmark-common-tasks TestUtil QueryParser ${REALM_WOLFSSL_LIB_D3})

add_dependencies(benchmarks realm-benchmark-common-tasks)
