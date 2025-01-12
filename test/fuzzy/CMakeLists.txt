set(TEST_AFL_SOURCES
    afl.cpp
    ../fuzz_group.cpp
) # TEST_AFL_SOURCES

set(TEST_LIBFUZZER_SOURCES
    libfuzzer_entry.cpp
    ../fuzz_group.cpp
) # TEST_LIBFUZZER_SOURCES

file(GLOB AFL_RUN_SCRIPTS
     "simple_start.sh"
     "simple_stop.sh"
     "start_parallel_fuzzer.sh"
     "stop_parallel_fuzzer.sh")

file(COPY ${AFL_RUN_SCRIPTS}
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

file(GLOB AFL_SEEDS "testcases/*")
file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/testcases)
file(COPY ${AFL_SEEDS}
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/testcases)

add_executable(fuzz-group ${TEST_AFL_SOURCES})
target_link_libraries(fuzz-group TestUtil Storage)

if("${CMAKE_GENERATOR}" MATCHES "Visual Studio")
    message(STATUS "fuzz-group target_include_directories not used for Visual Studio project. (see project properties)")
else()
    if(REALM_HAVE_WOLFSSL)
        message(STATUS "ObjectStore REALM_ENABLE_ENCRYPTION UNIX target_include_directories '${REALM_WOLFSSL_ROOT_DIR}/include'")
        target_include_directories(fuzz-group  PRIVATE ${REALM_WOLFSSL_ROOT_DIR}/include)
    else()
        message(STATUS "REALM_HAVE_WOLFSSL is not enabled for fuzz-group.")
    endif()
endif()

if(REALM_LIBFUZZER)
    add_executable(realm-libfuzzer ${TEST_LIBFUZZER_SOURCES})
    target_link_libraries(realm-libfuzzer TestUtil Storage)
endif()
