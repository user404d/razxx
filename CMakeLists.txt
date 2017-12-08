cmake_minimum_required(VERSION 3.1)
project(razxx CXX)

enable_testing()

set(VERSION "0.0.0")

option(RAZXX_BUILD_EXAMPLES "Set to ON to build examples" OFF)
option(RAZXX_BUILD_TESTS "Set to ON to build tests" OFF)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "[Cc]lang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Werror -Wextra -Wshadow -Wpedantic")
endif()

add_library(razxx INTERFACE)

target_include_directories(razxx INTERFACE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
    )

include(CMakePackageConfigHelpers)
set(RAZXX_CMAKE_DIR "lib/cmake/razxx" CACHE STRING
    "Installation directory for cmake files, relative to ${CMAKE_INSTALL_PREFIX}.")
set(version_config "${PROJECT_BINARY_DIR}/razxx-config-version.cmake")
set(project_config "${PROJECT_BINARY_DIR}/razxx-config.cmake")
set(targets_export_name razxx-targets)

write_basic_package_version_file(
    ${version_config}
    VERSION ${VERSION}
    COMPATIBILITY AnyNewerVersion)
configure_package_config_file(
    ${PROJECT_SOURCE_DIR}/razxx-config.cmake.in
    ${project_config}
    INSTALL_DESTINATION ${RAZXX_CMAKE_DIR})
export(TARGETS razxx NAMESPACE razxx::
    FILE ${PROJECT_BINARY_DIR}/${targets_export_name}.cmake)

install(
    FILES ${project_config} ${version_config}
    DESTINATION ${RAZXX_CMAKE_DIR})
install(EXPORT ${targets_export_name} DESTINATION ${RAZXX_CMAKE_DIR}
    NAMESPACE razxx::)

install(TARGETS razxx EXPORT ${targets_export_name} DESTINATION lib)
install(FILES ${PROJECT_SOURCE_DIR}/include/razxx.hpp DESTINATION include)

add_subdirectory(examples)
add_subdirectory(test)