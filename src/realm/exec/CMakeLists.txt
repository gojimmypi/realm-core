message(STATUS "\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")


add_executable(RealmImporter importer_tool.cpp importer.cpp importer.hpp)
set_target_properties(RealmImporter PROPERTIES
    OUTPUT_NAME "realm-importer"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)

# include_directories("/mnt/c/workspace/wolfssl/wolfssl" "/mnt/c/workspace/wolfssl")


 #   target_include_directories(RealmImporter PRIVATE
 #       /mnt/c/workspace/wolfssl
 #   )

message(STATUS "RealmImporter wolfssl: ${REALM_WOLFSSL_LIB_D3}")
target_link_libraries(RealmImporter Storage "${REALM_WOLFSSL_LIB_D3}")

if(NOT APPLE AND NOT ANDROID AND NOT CMAKE_SYSTEM_NAME MATCHES "^Windows")
    add_executable(RealmDaemon realmd.cpp)
    set_target_properties(RealmDaemon PROPERTIES
        OUTPUT_NAME "realmd"
        DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})
    target_link_libraries(RealmDaemon Storage "${REALM_WOLFSSL_LIB_D3}")
    list(APPEND ExecTargetsToInstall RealmDaemon)
endif()

add_executable(RealmTrawler realm_trawler.cpp)
set_target_properties(RealmTrawler PROPERTIES
    OUTPUT_NAME "realm-trawler"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmTrawler Storage "${REALM_WOLFSSL_LIB_D3}")
if (EMSCRIPTEN)
    set_target_properties(RealmTrawler PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmEnumerate realm_enumerate.cpp)
set_target_properties(RealmEnumerate PROPERTIES
    OUTPUT_NAME "realm-enumerate"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmEnumerate ObjectStore "${REALM_WOLFSSL_LIB_D3}")
# FIXME can be fixed for others, but requires link and install fixes for libuv target
if (NOT APPLE)
    set_target_properties(RealmEnumerate PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmDecrypt realm_decrypt.cpp)
set_target_properties(RealmDecrypt PROPERTIES
    OUTPUT_NAME "realm-decrypt"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmDecrypt Storage "${REALM_WOLFSSL_LIB_D3}")
if (NOT REALM_ENABLE_ENCRYPTION)
    set_target_properties(RealmDecrypt PROPERTIES EXCLUDE_FROM_ALL TRUE)
endif()

add_executable(RealmEncrypt realm_encrypt.cpp)
set_target_properties(RealmEncrypt PROPERTIES
    OUTPUT_NAME "realm-encrypt"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmEncrypt Storage "${REALM_WOLFSSL_LIB_D3}")

add_executable(RealmBrowser realm_browser.cpp)
set_target_properties(RealmBrowser PROPERTIES
    OUTPUT_NAME "realm-browser-10"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(RealmBrowser Storage "${REALM_WOLFSSL_LIB_D3}")

if(REALM_ENABLE_SYNC)
add_executable(Realm2JSON realm2json.cpp )
set_target_properties(Realm2JSON PROPERTIES
    OUTPUT_NAME "realm2json"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)
target_link_libraries(Realm2JSON Storage QueryParser Sync "${REALM_WOLFSSL_LIB_D3}")
list(APPEND ExecTargetsToInstall Realm2JSON)
endif()

if("${CMAKE_GENERATOR}" MATCHES "Visual Studio")
    message(STATUS "Realm[lib] target_include_directories not used for Visual Studio project. (see project properties)")
else()
    if(REALM_HAVE_WOLFSSL)
        message(STATUS "Realm[lib] target_include_directories '${REALM_WOLFSSL_ROOT_DIR}/include'")
        target_include_directories(RealmImporter  PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        if(NOT APPLE AND NOT ANDROID AND NOT CMAKE_SYSTEM_NAME MATCHES "^Windows")
            target_include_directories(RealmDaemon    PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        endif()
        target_include_directories(RealmEnumerate PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        target_include_directories(RealmTrawler   PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        target_include_directories(RealmDecrypt   PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        target_include_directories(RealmEncrypt   PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        target_include_directories(RealmBrowser   PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        target_include_directories(Realm2JSON     PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
        message(STATUS "")
    else()
        message(STATUS "REALM_HAVE_WOLFSSL is not enabled for Realm execs.")
    endif()
endif()

add_executable(RealmDump realm_dump.c)
set_target_properties(RealmDump PROPERTIES
    OUTPUT_NAME "realm-dump"
    DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)

install(TARGETS ${ExecTargetsToInstall}
        COMPONENT runtime
        DESTINATION bin)
