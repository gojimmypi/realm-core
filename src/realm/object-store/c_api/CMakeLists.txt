message("\n\n=======================================================\nCurrent CMake file: ${CMAKE_CURRENT_LIST_FILE}")


# add_library(wolfssl STATIC IMPORTED)
add_compile_definitions(WOLFSSL_USER_SETTINGS)
link_directories("/workspace/wolfssl/DLL Debug/x64")
include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")
set_target_properties(wolfssl PROPERTIES IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")
set(REALM_WOLFSSL_LIB "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")

if(REALM_HAVE_WOLFSSL)
    message(STATUS "test cmake: REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
    include_directories("${REALM_WOLFSSL_ROOT_DIR}/include")
    include_directories("/workspace/wolfssl/wolfssl" "/workspace/wolfssl" "/workspace/wolfssl/IDE/WIN")


    if(NOT TARGET wolfssl)
        message(STATUS ">>> add library wolfSSL!")
        add_library(wolfssl STATIC IMPORTED)
        set_target_properties(wolfssl PROPERTIES IMPORTED_LOCATION "/workspace/wolfssl/DLL Debug/x64/wolfssl.lib")
    else()
        message(STATUS ">>> Skip library wolfSSL! already found.")
    endif()

    #link_directories("${REALM_WOLFSSL_ROOT_DIR}/lib")
    link_directories("/workspace/wolfssl/DLL Debug/x64")

    # target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE wolfssl)


else()
    set(REALM_WOLFSSL_LIB "")
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
endif()

set(REALM_FFI_SOURCES
    ../../../realm.h

    config.cpp
    dictionary.cpp
    error.cpp
    logging.cpp
    notifications.cpp
    object.cpp
    list.cpp
    query.cpp
    realm.cpp
    scheduler.cpp
    schema.cpp
    set.cpp
    util.cpp

    conversion.hpp
    types.hpp
    util.hpp
    # realm/object-store/c_api/realm.c
)

if(REALM_ENABLE_SYNC)
    list(APPEND REALM_FFI_SOURCES
        app.cpp
        http.cpp
        sync.cpp
        socket_provider.cpp
    )
endif()

if(NOT EMSCRIPTEN)
    add_library(RealmFFI SHARED ${REALM_FFI_SOURCES})
else()
    add_executable(RealmFFI ${REALM_FFI_SOURCES})
endif()
add_library(RealmFFIStatic STATIC ${REALM_FFI_SOURCES})

target_compile_definitions(RealmFFI PRIVATE -DRealm_EXPORTS)
target_compile_definitions(RealmFFIStatic PUBLIC -DRLM_NO_DLLIMPORT)

target_link_libraries(RealmFFI PRIVATE Storage ObjectStore QueryParser "${REALM_WOLFSSL_LIB}")
target_link_libraries(RealmFFIStatic PRIVATE Storage ObjectStore QueryParser "${REALM_WOLFSSL_LIB}")

if (${REALM_ENABLE_SYNC})
    target_link_libraries(RealmFFI PRIVATE Sync)
    target_link_libraries(RealmFFIStatic PRIVATE Sync)
endif()

if (ANDROID)
    target_link_libraries(RealmFFI PUBLIC android log)
endif()

target_include_directories(RealmFFIStatic INTERFACE
    $<BUILD_INTERFACE:${RealmCore_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:include>
)

target_include_directories(RealmFFI INTERFACE
    $<BUILD_INTERFACE:${RealmCore_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:include>
)

set_target_properties(RealmFFI PROPERTIES
    OUTPUT_NAME "realm-ffi"
    CXX_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN ON

    # FIXME: Not building by default because of the link-time dependency on
    # libuv. This should be fixed by refactoring Scheduler to be a dynamic
    # interface with an implementation selected at runtime.
    EXCLUDE_FROM_ALL ON
)

set_target_properties(RealmFFIStatic PROPERTIES
    OUTPUT_NAME "realm-ffi-static"
    CXX_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN ON
)

# install(TARGETS RealmFFI DESTINATION lib COMPONENT devel)

install(TARGETS RealmFFIStatic DESTINATION lib COMPONENT devel)
install(FILES ../../../realm.h DESTINATION include COMPONENT devel)