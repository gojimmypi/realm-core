message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")
message(STATUS "REALM_WOLFSSL_LIB wolfssl: ${REALM_WOLFSSL_LIB}")

add_executable(realm-benchmark-crud main.cpp)
add_dependencies(benchmarks realm-benchmark-crud)
target_link_libraries(realm-benchmark-crud TestUtil "${REALM_WOLFSSL_LIB}")
