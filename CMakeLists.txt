# CMakeList.txt: проект CMake для CMakeProject1; включите исходный код и определения,
# укажите здесь логику для конкретного проекта.
#
cmake_minimum_required (VERSION 3.2)

project(project1 VERSION 0.0.1$ENV{TRAVIS_BUILD_NAMBER})

find_package(Threads REQUIRED)

#====================================================
# отвечает за тип желаемой линковки
set(Boost_USE_STATIC_LIBS   ON)
# многопоточная/однопоточная версии компонент(необяз.)
set(Boost_USE_MULTITHREADED ON)
#====================================================
find_package(Boost COMPONENTS unit_test_framework REQUIRED)

configure_file(version.h.in ${PROJECT_SOURCE_DIR}/version.h)
# Добавьте источник в исполняемый файл этого проекта.
add_executable (hello_world_cli "main.cpp" "main.h")

add_library(hello_world "lib.cpp" "lib.h")

add_executable(test_version "test_version.cpp")
# TODO: Добавьте тесты и целевые объекты, если это необходимо.

set_target_properties(hello_world_cli hello_world test_version PROPERTIES 
CXX_STANDARD 17
CXX_STANDARD_REQUIRED ON
COMPILE_OPTIONS "-Wpedantic; -Wall; -Wextra")

set_target_properties(test_version PROPERTIES
COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR})

target_link_libraries(hello_world_cli
hello_world
Threads::Threads)

target_link_libraries(test_version
${Boost_LIBRARIES}
hello_world)

install(TARGETS hello_world_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT ilianovi@mail.ru)

set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)

include(CPack)

enable_testing()

add_test(hello_world_tests test_version)


