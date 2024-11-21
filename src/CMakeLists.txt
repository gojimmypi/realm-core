    if(WOLFSSL_BREADCRUMB)
        message(STATUS "Found WOLFSSL_BREADCRUMB! in ${CMAKE_CURRENT_LIST_FILE}")
        message(STATUS "REALM_HAVE_WOLFSSL:${REALM_HAVE_WOLFSSL}")
        message(STATUS "REALM_HAVE_OPENSSL:${REALM_HAVE_OPENSSL}")
    else()
        message(STATUS "NOT Found WOLFSSL_BREADCRUMB! in ${CMAKE_CURRENT_LIST_FILE}")
    endif()

message("Current CMake file: ${CMAKE_CURRENT_LIST_FILE}")
# include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")

# link_directories("/workspace/wolfssl/DLL Debug/x64")
# add_compile_definitions(WOLFSSL_USER_SETTINGS)
# link_directories("/workspace/wolfssl/DLL Debug/x64")
# include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")
# set_target_properties(wolfssl PROPERTIES IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")

add_subdirectory(realm)
add_subdirectory(external/IntelRDFPMathLib20U2)

if (REALM_ENABLE_GEOSPATIAL)
    add_subdirectory(external/s2)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "^Windows")
  add_subdirectory(win32)
endif()

install(FILES realm.hpp
        DESTINATION include
        COMPONENT devel)
