cmake_minimum_required(VERSION 2.8)
project(gpio CXX C)

#Read version number and use it
execute_process(COMMAND ./newVersion
		WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
		OUTPUT_QUIET
		ERROR_QUIET)
file(READ version.h FILE_CONTENT)
string(REGEX MATCH "[0-9]*[.][0-9]*([.][0-9]*)?" VERSION_NUMBER ${FILE_CONTENT})
message(STATUS "Version number: ${VERSION_NUMBER}")

#Search for the wiringPi library
if(BUILD_SUPBARTS)
	find_package(Threads REQUIRED)
	include_directories(../wiringPi)
	set(LIBS wiringPi ${CMAKE_THREAD_LIBS_INIT})
	link_directories(${CMAKE_BINARY_DIR}/wiringPi)
else(BUILD_SUPBARTS)
	find_package(wiringPi REQUIRED)
	find_package(Threads REQUIRED)
	include_directories(${wiringPi_INCLUDE_DIRS})
	set(LIBS ${wiringPi_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})
endif(BUILD_SUPBARTS)

#Create an option to link 'gpio' to the wiringPiDev library that offers the support
#of the Gert board and PiFace board
option(ENABLE_GERTBOARD_COMPATIBILITY "Enable the GertBoard capabilities (NOT AVAILABLE NOW)" OFF)
if(ENABLE_GERTBOARD_COMPATIBILITY)
	message(FATAL_ERROR "The \"ENABLE_GERTBOARD_COMPATIBILITY\" option is not available now")
	add_definitions(-DENABLE_GERTBOARD_COMPATIBILITY)
endif(ENABLE_GERTBOARD_COMPATIBILITY)
option(ENABLE_PIFACE_COMPATIBILITY "Enable the PiFace capabilities (NOT AVAILABLE NOW)" OFF)
if(ENABLE_PIFACE_COMPATIBILITY)
	message(FATAL_ERROR "The \"ENABLE_PIFACE_COMPATIBILITY\" option is not available now")
	add_definitions(-DENABLE_PIFACE_COMPATIBILITY)
endif(ENABLE_PIFACE_COMPATIBILITY)

#Compile the gpio binary and install it
file(GLOB gpioSOURCES *.c)
add_executable(gpio ${gpioSOURCES})
target_link_libraries(gpio ${LIBS})
set_target_properties(gpio PROPERTIES VERSION ${VERSION_NUMBER})
install(TARGETS gpio RUNTIME DESTINATION bin)

#Install the pintest binary
install(PROGRAMS pintest DESTINATION bin)

#TODO FIX THIS PART
#Install the man page
#if(ENABLE_GERTBOARD_COMPATIBILITY AND ENABLE_PIFACE_COMPATIBILITY)
#	set(MAN_PAGE gpio.1.GERT-PIFACE.in)
#elseif(ENABLE_GERTBOARD_COMPATIBILITY)
#	set(MAN_PAGE gpio.1.GERT.in)
#elseif(ENABLE_PIFACE_COMPATIBILITY)
#	set(MAN_PAGE gpio.1.PIFACE.in)
#else(ENABLE_GERTBOARD_COMPATIBILITY AND ENABLE_PIFACE_COMPATIBILITY)
#	set(MAN_PAGE gpio.1.in)
#endif(ENABLE_GERTBOARD_COMPATIBILITY AND ENABLE_PIFACE_COMPATIBILITY)
#file(COPY ${MAN_PAGE} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
##file(RENAME ${CMAKE_CURRENT_BINARY_DIR}/${MAN_PAGE} ${CMAKE_CURRENT_BINARY_DIR}/gpio.1)
#install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${MAN_PAGE} DESTINATION share/man RENAME gpio.1)
##install pintest in bin
