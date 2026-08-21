cmake_minimum_required(VERSION 4.0)
project(myproject LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(MYPROJECT_BUILD_TESTS "Build myproject unit tests" ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

include(FetchContent)

# Eigen and other dependencies; skip eigen tests
FetchContent_Declare(
    eigen
    GIT_REPOSITORY https://gitlab.com/libeigen/eigen.git
    GIT_TAG        5.0.1
    SOURCE_SUBDIR  cmake-ignore   # deliberately nonexistent -- see comment above
)
FetchContent_MakeAvailable(eigen)

add_library(Eigen3::Eigen INTERFACE IMPORTED)
target_include_directories(Eigen3::Eigen INTERFACE ${eigen_SOURCE_DIR})

# --- Core library, included by main and tests
add_library(myproject_core STATIC
    src/module.cpp
)
target_include_directories(myproject_core PUBLIC ${CMAKE_SOURCE_DIR}/src)
target_link_libraries(myproject_core PUBLIC Eigen3::Eigen)

# --- Executable ---
add_executable(myproject
    src/main.cpp
)
target_link_libraries(myproject PRIVATE myproject_core)

target_compile_options(myproject PRIVATE
    $<$<CONFIG:Release>:-O3 -march=native>
)

# Unit tests using doctest
if(MYPROJECT_BUILD_TESTS)
    FetchContent_Declare(
        doctest
        GIT_REPOSITORY https://github.com/doctest/doctest.git
        GIT_TAG        v2.4.11
        SOURCE_SUBDIR  cmake-ignore   # deliberately nonexistent -- see Eigen comment above
    )
    FetchContent_MakeAvailable(doctest)

    add_library(doctest::doctest INTERFACE IMPORTED)
    target_include_directories(doctest::doctest INTERFACE ${doctest_SOURCE_DIR}/doctest)

    add_executable(unit_test
        test/unit/main.test.cpp
        test/unit/module.test.cpp)
    target_link_libraries(unit_test PRIVATE myproject_core doctest::doctest)

    # Coverage instrumentation
    option(MYPROJECT_ENABLE_COVERAGE "Build unit_test with coverage instrumentation" OFF)
    if(MYPROJECT_ENABLE_COVERAGE)
        target_compile_options(unit_test PRIVATE --coverage -O0 -g)
        target_link_options(unit_test PRIVATE --coverage)
    endif()
endif()

# --- Documentation (Doxygen) ---
option(MYPROJECT_BUILD_DOCS "Add a 'docs' target that generates Doxygen HTML documentation" OFF)
if(MYPROJECT_BUILD_DOCS)
    find_package(Doxygen REQUIRED OPTIONAL_COMPONENTS dot)
    if(Doxygen_dot_FOUND)
        set(MYPROJECT_HAVE_DOT YES)
    else()
        set(MYPROJECT_HAVE_DOT NO)
        message(WARNING "Graphviz 'dot' not found; the 'docs' target will skip dependency graphs")
    endif()

    # doxygen-awesome-css gives the generated HTML a modern look & feel.
    FetchContent_Declare(
        doxygen_awesome_css
        GIT_REPOSITORY https://github.com/jothepro/doxygen-awesome-css.git
        GIT_TAG        v2.3.4
        SOURCE_SUBDIR  cmake-ignore   # deliberately nonexistent -- see Eigen comment above
    )
    FetchContent_MakeAvailable(doxygen_awesome_css)

    set(MYPROJECT_DOCS_DIR ${CMAKE_SOURCE_DIR}/docs)
    set(MYPROJECT_DOCS_OUTPUT_DIR ${CMAKE_BINARY_DIR}/docs)
    set(DOXYGEN_AWESOME_CSS_DIR ${doxygen_awesome_css_SOURCE_DIR})

    configure_file(
        ${MYPROJECT_DOCS_DIR}/Doxyfile.in
        ${CMAKE_BINARY_DIR}/Doxyfile
        @ONLY
    )

    add_custom_target(docs
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_BINARY_DIR}/Doxyfile
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
        COMMENT "Generating Doxygen documentation in ${MYPROJECT_DOCS_OUTPUT_DIR}/html"
        VERBATIM
    )
endif()
