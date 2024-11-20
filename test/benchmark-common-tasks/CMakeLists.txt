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

add_executable(realm-benchmark-common-tasks main.cpp)

if(REALM_HAVE_WOLFSSL)
	message(STATUS "benchmark common tasks cmake: ")
	# set in parent cmake:
	message(STATUS "Found REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
	message(STATUS "Found REALM_WOLFSSL_LIB=${REALM_WOLFSSL_LIB}")
else()
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

target_link_libraries(realm-benchmark-common-tasks TestUtil QueryParser ${REALM_WOLFSSL_LIB})

add_dependencies(benchmarks realm-benchmark-common-tasks)
