cmake_minimum_required(VERSION 3.15)
project(tulipindicators VERSION 0.9.1)

set(CMAKE_C_STANDARD 98)
set(CMAKE_C_STANDARD_REQUIRED ON)

option(TULIPINDICATORS_BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(TULIPINDICATORS_BUILD_EXAMPLES "Build the examples" ON)

set(HEADER_FILES ${CMAKE_CURRENT_LIST_DIR}/indicators.h
                 ${CMAKE_CURRENT_LIST_DIR}/candles.h)

file(GLOB_RECURSE INDICATORS_SOURCES ${CMAKE_CURRENT_LIST_DIR}/indicators/*.c)

set(SOURCE_FILES
    ${CMAKE_CURRENT_LIST_DIR}/indicators.c ${CMAKE_CURRENT_LIST_DIR}/candles.c
    ${CMAKE_CURRENT_LIST_DIR}/tiamalgamation.c ${INDICATORS_SOURCES})

set(VERSION_SHORT
    "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}"
)

if(TULIPINDICATORS_BUILD_SHARED_LIBS)
  add_library(${PROJECT_NAME} ${SOURCE_FILES} ${HEADER_FILES})
else()
  add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES} ${HEADER_FILES})
endif()

target_include_directories(${PROJECT_NAME}
                           PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/indicators)

set_target_properties(
  ${PROJECT_NAME}
  PROPERTIES C_EXTENSIONS OFF
             EXPORT_NAME ${PROJECT_NAME}
             VERSION ${VERSION_SHORT}
             ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
             LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
             RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")
set_target_properties(${PROJECT_NAME} PROPERTIES C_STANDARD 17
                                                 C_STANDARD_REQUIRED ON)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${VERSION_SHORT}
  COMPATIBILITY SameMajorVersion)
install(
  FILES ${HEADER_FILES}
  DESTINATION include
  COMPONENT headers)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}Targets
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  INCLUDES
  DESTINATION include)

install(
  EXPORT ${PROJECT_NAME}Targets
  FILE ${PROJECT_NAME}Targets.cmake
  NAMESPACE ${PROJECT_NAME}::
  DESTINATION lib/cmake/${PROJECT_NAME})

install(
  FILES ${PROJECT_NAME}Config.cmake
  RENAME ${PROJECT_NAME}Config.cmake
  DESTINATION lib/cmake/${PROJECT_NAME})

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
        DESTINATION lib/cmake/${PROJECT_NAME})

target_include_directories(
  ${PROJECT_NAME} PUBLIC $<INSTALL_INTERFACE:include>
                         $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)
