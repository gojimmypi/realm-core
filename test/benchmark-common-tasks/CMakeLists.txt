add_executable(realm-benchmark-common-tasks main.cpp)

if(REALM_HAVE_WOLFSSL)
	message(STATUS "benchmark common tasks cmake: REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
	include_directories("${REALM_WOLFSSL_ROOT_DIR}/include")
	link_directories("${REALM_WOLFSSL_ROOT_DIR}/lib")
	find_library(WOLFSSL_LIB NAMES wolfssl PATHS "${REALM_WOLFSSL_ROOT_DIR}/lib")
	message(status "WOLFSSL_LIB:${WOLFSSL_LIB}")
else()
	set(WOLFSSL_LIB, "")
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

target_link_libraries(realm-benchmark-common-tasks TestUtil QueryParser ${WOLFSSL_LIB})

add_dependencies(benchmarks realm-benchmark-common-tasks)
