cmake_minimum_required(VERSION 3.16..3.25)

project(my_project
    VERSION 0.0.1
    LANGUAGES CXX
)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

option(MYPROJECT_BUILD_TESTS "Enable building the tests" OFF)
option(MYPROJECT_BUILD_MAIN "Enable building the executable" OFF)

set(is_gcc $<CXX_COMPILER_ID:GNU>)
set(is_clang $<CXX_COMPILER_ID:Clang,AppleClang>)
set(is_msvc $<CXX_COMPILER_ID:MSVC>)
set(debug_compile_options
    $<${is_gcc}:-Wall -Wextra -pedantic-errors>
    $<${is_clang}:-Wall -Wextra -pedantic-errors>
    $<${is_msvc}:/W4>
)
option(MYPROJECT_WERROR
    "Treat warning as errors (ignored if CMAKE_BUILD_TYPE is not Debug)"
    OFF
)
set(is_werror $<AND:$<CONFIG:Debug>,$<BOOL:${MYPROJECT_WERROR}>>)
set(werror_compile_option
    $<$<OR:${is_gcc},${is_clang}>:-Werror>
    $<${is_msvc}:/WX>
)
set(w_compile_options
    $<$<CONFIG:Debug>:${debug_compile_options}>
    $<${is_werror}:${werror_compile_option}>
)

add_library(my_lib
    src/my_lib.cpp
)
# How to prevent warnings in header files (when used in other projects):
# https://www.foonathan.net/2018/10/cmake-warnings
target_include_directories(my_lib PRIVATE
    include/
)
target_include_directories(my_lib SYSTEM INTERFACE
    include/
)

target_compile_options(my_lib PRIVATE
    ${w_compile_options}
)
target_compile_definitions(my_lib PRIVATE $<$<CONFIG:Debug>:DEBUG>)

add_custom_target(copy_resources ALL
    COMMAND ${CMAKE_COMMAND} -E copy_directory
            ${CMAKE_CURRENT_SOURCE_DIR}/res
            ${CMAKE_CURRENT_BINARY_DIR}/res
    COMMENT "Copying resources into build directory"
)

IF (MYPROJECT_BUILD_MAIN)
    add_executable(main src/main.cpp)
    target_link_libraries(main PRIVATE
        my_lib
    )
    target_compile_options(main PRIVATE
        ${w_compile_options}
    )
    target_compile_definitions(main PRIVATE $<$<CONFIG:Debug>:DEBUG>)
    add_dependencies(main copy_resources)
endif()

if (MYPROJECT_BUILD_TESTS)
    add_subdirectory(tests)
endif()
