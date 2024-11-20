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
    include_directories("${REALM_WOLFSSL_ROOT_DIR}/include")
    include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")

    set_target_properties(wolfssl PROPERTIES
        IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")

    #link_directories("${REALM_WOLFSSL_ROOT_DIR}/lib")
    link_directories("/workspace/wolfssl/DLL Debug/x64")
else()
    set(REALM_WOLFSSL_LIB "")
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

add_executable(RealmImporter importer_tool.cpp importer.cpp importer.hpp)
set_target_properties(RealmImporter PROPERTIES
    OUTPUT_NAME "realm-importer"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_include_directories(RealmImporter PRIVATE
    /workspace/wolfssl
    /workspace/wolfssl/IDE/WIN
    C:/workspace/osp-gojimmypi/realm/realm-core-gojimmypi/src
    C:/workspace/osp-gojimmypi/realm/VS2022/src
)
message(STATUS "RealmImporter wolfssl: ${REALM_WOLFSSL_LIB}")
target_link_libraries(RealmImporter Storage "${REALM_WOLFSSL_LIB}")

if(NOT APPLE AND NOT ANDROID AND NOT CMAKE_SYSTEM_NAME MATCHES "^Windows")
    add_executable(RealmDaemon realmd.cpp)
    set_target_properties(RealmDaemon PROPERTIES
        OUTPUT_NAME "realmd"
        DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})
    target_link_libraries(RealmDaemon Storage "${REALM_WOLFSSL_LIB}")
    list(APPEND ExecTargetsToInstall RealmDaemon)
endif()

add_executable(RealmTrawler realm_trawler.cpp)
set_target_properties(RealmTrawler PROPERTIES
    OUTPUT_NAME "realm-trawler"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmTrawler Storage "${REALM_WOLFSSL_LIB}")
if (EMSCRIPTEN)
    set_target_properties(RealmTrawler PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmEnumerate realm_enumerate.cpp)
set_target_properties(RealmEnumerate PROPERTIES
    OUTPUT_NAME "realm-enumerate"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmEnumerate ObjectStore "${REALM_WOLFSSL_LIB}")
# FIXME can be fixed for others, but requires link and install fixes for libuv target
if (NOT APPLE)
    set_target_properties(RealmEnumerate PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmDecrypt realm_decrypt.cpp)
set_target_properties(RealmDecrypt PROPERTIES
    OUTPUT_NAME "realm-decrypt"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmDecrypt Storage "${REALM_WOLFSSL_LIB}")
if (NOT REALM_ENABLE_ENCRYPTION)
    set_target_properties(RealmDecrypt PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmEncrypt realm_encrypt.cpp)
set_target_properties(RealmEncrypt PROPERTIES
    OUTPUT_NAME "realm-encrypt"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmEncrypt Storage "${REALM_WOLFSSL_LIB}")

add_executable(RealmBrowser realm_browser.cpp)
set_target_properties(RealmBrowser PROPERTIES
    OUTPUT_NAME "realm-browser-10"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmBrowser Storage "${REALM_WOLFSSL_LIB}")

if(REALM_ENABLE_SYNC)
add_executable(Realm2JSON realm2json.cpp )
set_target_properties(Realm2JSON PROPERTIES
    OUTPUT_NAME "realm2json"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(Realm2JSON Storage QueryParser Sync "${REALM_WOLFSSL_LIB}")
list(APPEND ExecTargetsToInstall Realm2JSON)
endif()

add_executable(RealmDump realm_dump.c)
set_target_properties(RealmDump PROPERTIES
    OUTPUT_NAME "realm-dump"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)

install(TARGETS ${ExecTargetsToInstall}
        COMPONENT runtime
        DESTINATION bin)
