cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
set(PROJECT_NAME clinfo)

project(${PROJECT_NAME} C)

if ((NOT MSVC) AND (NOT CMAKE_BUILD_TYPE))
  set (CMAKE_BUILD_TYPE Release)
endif()

if (CMAKE_BUILD_TYPE)
  message(STATUS "Build configuration: ${CMAKE_BUILD_TYPE}")
endif()

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules")

find_package(OpenCL REQUIRED)

set(OCLTEST_SRCS
  src/clinfo.c

  src/ctx_prop.h
  src/error.h
  src/ext.h
  src/fmtmacros.h
  src/info_loc.h
  src/info_ret.h
  src/memory.h
  src/ms_support.h
  src/opt_out.h
  src/strbuf.h
  )

if (NOT MSVC)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wextra")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pedantic")

  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -O0")
endif()

add_executable(${PROJECT_NAME} ${OCLTEST_SRCS})

if (UNIX AND (NOT APPLE))
  target_link_libraries(${PROJECT_NAME}
    ${OpenCL_LIBRARY}
    "-ldl"
    )
endif()

target_include_directories(${PROJECT_NAME} PUBLIC ${OpenCL_INCLUDE_DIR})
target_link_libraries(${PROJECT_NAME} ${OpenCL_LIBRARY})

# Do not add an rpath to any of the binaries
set(CMAKE_SKIP_RPATH true)

install(TARGETS ${PROJECT_NAME}
  DESTINATION bin
  )
