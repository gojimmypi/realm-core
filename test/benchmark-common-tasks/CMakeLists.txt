if(REALM_HAVE_WOLFSSL)
	message(STATUS "benchmark common tasks cmake: REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
else()
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

add_executable(realm-benchmark-common-tasks main.cpp)
include_directories("/home/gojimmypi/wolfssl-install-dir/include")
link_directories("/home/gojimmypi/wolfssl-install-dir/lib")
find_library(WOLFSSL_LIB NAMES wolfssl PATHS "/home/gojimmypi/wolfssl-install-dir/lib")
target_link_libraries(realm-benchmark-common-tasks TestUtil QueryParser  ${WOLFSSL_LIB})

add_dependencies(benchmarks realm-benchmark-common-tasks)
