message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")


# add_library(wolfssl STATIC IMPORTED)
add_compile_definitions(WOLFSSL_USER_SETTINGS)
link_directories("/workspace/wolfssl/DLL Debug/x64")
include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")
set_target_properties(wolfssl PROPERTIES IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")
set(REALM_WOLFSSL_LIB "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")

if(REALM_HAVE_WOLFSSL)
    message(STATUS "exec cmake: ${REALM_WOLFSSL_ROOT_DIR}")
    message(STATUS "REALM_WOLFSSL_LIB=${REALM_WOLFSSL_LIB}")

    include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")

    set_target_properties(wolfssl PROPERTIES
        IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")

    #link_directories("${REALM_WOLFSSL_ROOT_DIR}/lib")
    link_directories("/workspace/wolfssl/DLL Debug/x64")
else()
    set(REALM_WOLFSSL_LIB "")
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

add_executable(realm-benchmark-crud main.cpp)
add_dependencies(benchmarks realm-benchmark-crud)
target_link_libraries(realm-benchmark-crud TestUtil "${REALM_WOLFSSL_LIB}")
