message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")
# benchmark crud
message(STATUS "CMAKE_MODULE_PATH=${CMAKE_MODULE_PATH}")
message(STATUS "wolfSSL_DIR=${wolfSSL_DIR}")

message(STATUS "REALM_WOLFSSL_LIB wolfssl: ${REALM_WOLFSSL_LIB}")
message(STATUS "REALM_WOLFSSL_LIB_D1 wolfssl: ${REALM_WOLFSSL_LIB_D1}")
message(STATUS "REALM_WOLFSSL_LIB_D2 wolfssl: ${REALM_WOLFSSL_LIB_D2}")
message(STATUS "REALM_WOLFSSL_LIB_D3 wolfssl  ${REALM_WOLFSSL_LIB_D3}")
# find_package(wolfSSL REQUIRED)
add_executable(realm-benchmark-crud main.cpp)
add_dependencies(benchmarks realm-benchmark-crud)
target_link_libraries(realm-benchmark-crud TestUtil ${REALM_WOLFSSL_LIB_D2} )
