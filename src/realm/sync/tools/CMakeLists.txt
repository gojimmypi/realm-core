if(REALM_HAVE_WOLFSSL)
    message(STATUS "tools cmake: REALM_WOLFSSL_ROOT_DIR=${REALM_WOLFSSL_ROOT_DIR}")
    message(STATUS "Found REALM_HAVE_WOLFSSL")

    link_directories("${REALM_WOLFSSL_ROOT_DIR}/")
    link_directories("${REALM_WOLFSSL_ROOT_DIR}/lib")

    #include_directories("${REALM_WOLFSSL_ROOT_DIR}/include")
else()
    message(STATUS "WARNING: REALM_HAVE_WOLFSSL not set")
    set(REALM_WOLFSSL_LIB "")
endif()

add_executable(InspectorInspectClientRealm "inspect_client_realm.cpp")
set_target_properties(InspectorInspectClientRealm PROPERTIES
                      OUTPUT_NAME "realm-inspect-client-realm"
                      DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")
target_link_libraries(InspectorInspectClientRealm Sync Storage "${REALM_WOLFSSL_LIB}")

add_executable(InspectorPrintChangeset "print_changeset.cpp")
set_target_properties(InspectorPrintChangeset PROPERTIES
                      OUTPUT_NAME "realm-print-changeset"
                      DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")
target_link_libraries(InspectorPrintChangeset Sync Storage "${REALM_WOLFSSL_LIB}")

add_executable(ApplyToStateCommand apply_to_state_command.cpp)
set_target_properties(ApplyToStateCommand PROPERTIES
                      OUTPUT_NAME "realm-apply-to-state"
                      DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")
target_link_libraries(ApplyToStateCommand Sync Storage "${REALM_WOLFSSL_LIB}")

add_executable(HistCommand hist_command.cpp)
set_target_properties(HistCommand PROPERTIES
                      OUTPUT_NAME "realm-hist"
                      DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")
target_link_libraries(HistCommand Sync Storage "${REALM_WOLFSSL_LIB}")

install(TARGETS
    HistCommand
    ApplyToStateCommand
    InspectorInspectClientRealm
    InspectorPrintChangeset
    COMPONENT runtime
    DESTINATION bin
)
