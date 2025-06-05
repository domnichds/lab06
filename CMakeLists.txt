cmake_minimum_required(VERSION 3.10)

project(lab06)

option(BUILD_TESTS "Build tests" ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_lib)
add_subdirectory(solver_application)

install(TARGETS solver
        RUNTIME DESTINATION bin
        COMPONENT solver)

if(BUILD_TESTS)
  enable_testing()

  include(FetchContent)
  FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/release-1.12.1.zip
  )
  set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
  FetchContent_MakeAvailable(googletest)

  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check
    formatter
    formatter_ex
    solver_lib
    gtest_main
  )
  add_test(NAME check COMMAND check)
endif()

# CPack configuration
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_NAME "solver")
set(CPACK_PACKAGE_VERSION "1.0.0")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Solver Application")
set(CPACK_PACKAGE_VENDOR "Your Name")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "solver")

set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)

# DEB package configuration
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Your Name")
set(CPACK_DEBIAN_PACKAGE_SECTION "devel")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6")

# RPM package configuration
set(CPACK_RPM_PACKAGE_RELEASE "1")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "Development/Tools")

# Windows MSI configuration
set(CPACK_WIX_UPGRADE_GUID "12345678-1234-1234-1234-123456789012")
set(CPACK_WIX_PRODUCT_NAME "Solver")
set(CPACK_WIX_PRODUCT_VERSION "${CPACK_PACKAGE_VERSION}")
set(CPACK_WIX_PRODUCT_ICON "${CMAKE_CURRENT_SOURCE_DIR}/preview.png")
set(CPACK_WIX_UI_BANNER "${CMAKE_CURRENT_SOURCE_DIR}/preview.png")
set(CPACK_WIX_UI_DIALOG "${CMAKE_CURRENT_SOURCE_DIR}/preview.png")
set(CPACK_WIX_LICENSE_RTF "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE.rtf")

# macOS DMG configuration
set(CPACK_DMG_VOLUME_NAME "Solver")
set(CPACK_DMG_FORMAT "UDZO")
set(CPACK_DMG_BACKGROUND_IMAGE "${CMAKE_CURRENT_SOURCE_DIR}/preview.png")

# Set the package generators based on platform
if(WIN32)
  set(CPACK_GENERATOR "WIX")
elseif(APPLE)
  set(CPACK_GENERATOR "DragNDrop")
elseif(UNIX)
  if(EXISTS "/etc/debian_version")
    set(CPACK_GENERATOR "DEB")
  elseif(EXISTS "/etc/redhat-release")
    set(CPACK_GENERATOR "RPM")
  else()
    set(CPACK_GENERATOR "TGZ")
  endif()
endif()

# Set the package file name
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}")

# Set the package source directory
set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")
set(CPACK_SOURCE_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-src")

include(CPack)
