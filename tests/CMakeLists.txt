add_subdirectory(tools/Catch2)
add_subdirectory(tools/trompeloeil)

option(MYPROJECT_VERBOSE_TESTS
    "Should the output from the tests themselve be more verbose?"
    OFF
)
IF (MYPROJECT_VERBOSE_TESTS)
    set(TEST_ARG ${TEST_ARG} "-s")
endif()

option(MYPROJECT_TESTS_COLOR 
    "Should the output from the tests themselve be in color?"
    ON
)
if (MYPROJECT_TESTS_COLOR)
    set(TEST_ARG ${TEST_ARG} "--colour-mode=ansi")
endif()

enable_testing()

add_executable(my_test
    src/my_test.cpp
)
target_link_libraries(my_test PRIVATE
    my_lib
    Catch2::Catch2WithMain
    trompeloeil::trompeloeil
)
add_test(test_my_test my_test ${TEST_ARG})
