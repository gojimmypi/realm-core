message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")
message(STATUS "REALM_WOLFSSL_LIB wolfssl: ${REALM_WOLFSSL_LIB}")

add_executable(realm-benchmark-larger EXCLUDE_FROM_ALL main.cpp)
add_dependencies(benchmarks realm-benchmark-larger)
target_link_libraries(realm-benchmark-larger TestUtil "${REALM_WOLFSSL_LIB}")
