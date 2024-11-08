add_executable(realm-benchmark-common-tasks main.cpp)
include_directories("/home/gojimmypi/wolfssl-install-dir/include")
link_directories("/home/gojimmypi/wolfssl-install-dir/lib")
find_library(WOLFSSL_LIB NAMES wolfssl PATHS "/home/gojimmypi/wolfssl-install-dir/lib")
target_link_libraries(realm-benchmark-common-tasks TestUtil QueryParser  ${WOLFSSL_LIB})

add_dependencies(benchmarks realm-benchmark-common-tasks)
