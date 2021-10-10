cmake_minimum_required (VERSION 3.16)
project (trejnisto C)
SET(CMAKE_C_COMPILER "gyc")
if (CMAKE_BUILD_TYPE STREQUAL "RELEASE")
  set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -O3")
else () 
  set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -ggdb -O0")
endif ()

set (INSTALL_DIR "${CMAKE_CURRENT_SOURCE_DIR}/dist/${CMAKE_BUILD_TYPE}")

FILE (
  GLOB_RECURSE
  SRC_ENGINE
  engine/*.yr
  )

FILE (
  GLOB_RECURSE
  SRC_TEST
  test/fst/*.yr
  )

FILE (
  GLOB_RECURSE
  SRC_TEST_MISC
  test/misc/*.yr
  )


if (CMAKE_BUILD_TYPE STREQUAL "RELEASE")
  message ("-- Release mode")
elseif (CMAKE_BUILD_TYPE STREQUAL "DEBUG")
  message("-- Debug mode")
  add_compile_definitions(DEBUG=1)
elseif (CMAKE_BUILD_TYPE STREQUAL "MEM_DEBUG")
  message("-- Memory debug mode")
  add_compile_definitions(DEBUG=1)
  add_compile_definitions(MEM_DEBUG=1)
elseif (CMAKE_BUILD_TYPE STREQUAL "BUFFER_DEBUG")
  message("-- Buffer debug mode")
  add_compile_definitions(DEBUG=1)
  add_compile_definitions(BUFFER_DEBUG=1)
else ()
  message (FATAL_ERROR "compile mode must be specified -DCMAKE_BUILD_TYPE={DEBUG, MEM_DEBUG, RELEASE}")
endif () 

find_package(Bullet REQUIRED)
include_directories(${BULLET_INCLUDE_DIR})

SET_SOURCE_FILES_PROPERTIES(${SRC_ENGINE} ${SRC_TEST} ${SRC_TEST_MISC} PROPERTIES LANGUAGE C)


add_library(${PROJECT_NAME}_engine STATIC ${SRC_ENGINE})
target_link_libraries (${PROJECT_NAME}_engine -lSDL2 -lSDL2_image -lSDL2_ttf -lpthread -lGL -lassimp ${BULLET_LIBRARIES} -lm)

add_executable(${PROJECT_NAME}_tests ${SRC_TEST})
target_link_libraries (${PROJECT_NAME}_tests PUBLIC ${PROJECT_NAME}_engine)

add_executable(misc_${PROJECT_NAME} ${SRC_TEST_MISC})
target_link_libraries (misc_${PROJECT_NAME} PUBLIC ${PROJECT_NAME}_engine)

include_directories(${CMAKE_SOURCE_DIR}/engine/)
