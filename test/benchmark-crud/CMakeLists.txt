message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")
# benchmark crud
message(STATUS "CMAKE_MODULE_PATH=${CMAKE_MODULE_PATH}")
message(STATUS "wolfSSL_DIR=${wolfSSL_DIR}")

message(STATUS "REALM_WOLFSSL_LIB wolfssl 2: ${REALM_WOLFSSL_LIB}")
# find_package(wolfSSL REQUIRED)
add_executable(realm-benchmark-crud main.cpp)
add_dependencies(benchmarks realm-benchmark-crud)
target_link_libraries(realm-benchmark-crud TestUtil "${REALM_WOLFSSL_LIB}")
