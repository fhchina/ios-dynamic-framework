cmake_minimum_required(VERSION 3.0)
project(Bar VERSION 1.2.3)

find_package(Boo CONFIG REQUIRED)
find_package(Foo CONFIG REQUIRED)

add_library(bar SHARED bar.cpp)
target_link_libraries(bar PUBLIC Foo::foo Boo::boo)

set_target_properties(
    bar
    PROPERTIES
    INSTALL_NAME_DIR "@rpath/bar.framework"
    INSTALL_RPATH "@executable_path/Frameworks;@loader_path/Frameworks"
)

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

configure_file("cmake/Config.cmake.in" "${project_config}" @ONLY)

install(
    TARGETS bar
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)