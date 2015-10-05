cmake_minimum_required(VERSION 2.8)
project(wiringPi CXX C)

#Read version number and use it
file(READ ../VERSION FILE_CONTENT)
string(REGEX MATCH "[0-9]*[.][0-9]*([.][0-9]*)?" VERSION_NUMBER ${FILE_CONTENT})
message(STATUS "Version number: ${VERSION_NUMBER}")

#Add some flags
set(CMAKE_CXX_FLAGS "-Wall -Werror")

#Compile the library
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
file(GLOB wiring_SRC *.c)
add_library(wiringPi SHARED ${wiring_SRC})
set_target_properties(wiringPi PROPERTIES VERSION ${VERSION_NUMBER})
set(wiring_LIBRARIES wiringPi)

#Install the files
file(GLOB wiring_HEADERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.h)
install(FILES ${wiring_HEADERS} DESTINATION include/wiringPi)

install(TARGETS wiringPi LIBRARY DESTINATION lib)

configure_file(wiringPi.pc.in ${CMAKE_CURRENT_BINARY_DIR}/wiringPi.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/wiringPi.pc DESTINATION lib/pkgconfig)

configure_file(FindwiringPi.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/FindwiringPi.cmake @ONLY)
#TODO Improve to use the CMake version a bit better, i.e. if CMake is higher than 2.8 if will be installed in ${PREFIX}/share/cmake-<version>/Modules
if(CMAKE_CACHE_MAJOR_VERSION EQUAL 2 AND CMAKE_CACHE_MINOR_VERSION EQUAL 8)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/FindwiringPi.cmake DESTINATION ${CMAKE_INSTALL_PREFIX}/share/cmake-2.8/Modules)
else(CMAKE_CACHE_MAJOR_VERSION EQUAL 2 AND CMAKE_CACHE_MINOR_VERSION EQUAL 8)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/FindwiringPi.cmake DESTINATION ${CMAKE_INSTALL_PREFIX}/share/cmake/Modules)
endif(CMAKE_CACHE_MAJOR_VERSION EQUAL 2 AND CMAKE_CACHE_MINOR_VERSION EQUAL 8)
