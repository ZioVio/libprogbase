cmake_minimum_required(VERSION 2.8.7)

set(CMAKE_FIND_LIBRARY_PREFIXES "lib")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".a")

include_directories(include include/progbase include/progbase/collections)
file(GLOB LIB_SOURCES_COMMON "src/*.c")
if (UNIX)
    file(GLOB LIB_SOURCES_SPECIFIC "src/linux/*.c")
endif (UNIX)
if (WIN32)
    file(GLOB LIB_SOURCES_SPECIFIC "src/windows/*.c")
endif (WIN32)

file(GLOB LIB_SOURCES_COLLECTIONS "src/collections/*.c")
if (NOT ENABLE_EVENTS2)
    MESSAGE(STATUS "Events enabled")
    file(GLOB LIB_SOURCES_EVENTS "src/events/events.c")
endif (NOT ENABLE_EVENTS2)
if (ENABLE_EVENTS2)
    MESSAGE(STATUS "Events2 enabled")
    file(GLOB LIB_SOURCES_EVENTS "src/events/events2.c")
endif (ENABLE_EVENTS2)

project (progbase)

add_library(${CMAKE_PROJECT_NAME} STATIC 
    ${LIB_SOURCES_COMMON} 
    ${LIB_SOURCES_SPECIFIC} 
    ${LIB_SOURCES_COLLECTIONS}
    ${LIB_SOURCES_EVENTS})

set_property(TARGET ${CMAKE_PROJECT_NAME} PROPERTY C_STANDARD 99)

install(
    DIRECTORY include/ DESTINATION /usr/local/include
    FILES_MATCHING PATTERN "*.h"
)
install(TARGETS ${CMAKE_PROJECT_NAME} DESTINATION /usr/local/lib)

# flags
if (UNIX)
    SET(CMAKE_C_FLAGS "-std=gnu99 -Wall -pedantic-errors")
endif (UNIX)
if (WIN32)
    SET(CMAKE_C_FLAGS "-std=c99 -Wall -pedantic-errors")
endif (WIN32)

# Set default compile flags for GCC
if(CMAKE_COMPILER_IS_GNUC)
    message(STATUS "GCC detected, adding compile flags")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Werror -Wno-unused")
endif (CMAKE_COMPILER_IS_GNUC)