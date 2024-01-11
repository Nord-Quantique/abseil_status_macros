# GoogleTest (unit tests).
FetchContent_Declare(
        gtest
        SYSTEM
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG v1.13.0
)
FetchContent_MakeAvailable(gtest)
include(GoogleTest)

add_executable(abseil_status_macros_tests
        "status_macros_test.cc"
)

target_link_libraries(abseil_status_macros_tests PRIVATE absl_status_macros)
target_include_directories(abseil_status_macros_tests PRIVATE ${CMAKE_SOURCE_DIR})
target_link_libraries(abseil_status_macros_tests PRIVATE GTest::gtest_main GTest::gmock)
gtest_discover_tests(abseil_status_macros_tests)
