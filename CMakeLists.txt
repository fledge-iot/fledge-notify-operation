cmake_minimum_required(VERSION 2.6.0)

# Set the plugin name to build
project(operation)

# Supported options:
# -DFLEDGE_INCLUDE
# -DFLEDGE_LIB
# -DFLEDGE_SRC
# -DFLEDGE_INSTALL
#
# If no -D options are given and FLEDGE_ROOT environment variable is set
# then Fledge libraries and header files are pulled from FLEDGE_ROOT path.

set(CMAKE_CXX_FLAGS_DEBUG "-O0 -ggdb")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O3")

# Handling of Simple web server with compiler pre-processor macro RHEL_CENTOS_7
EXECUTE_PROCESS( COMMAND grep -o ^NAME=.* /etc/os-release COMMAND cut -f2 -d\" COMMAND sed s/\"//g OUTPUT_VARIABLE os_name )
EXECUTE_PROCESS( COMMAND grep -o ^VERSION_ID=.* /etc/os-release COMMAND cut -f2 -d\" COMMAND sed s/\"//g OUTPUT_VARIABLE os_version )

if ( ( ${os_name} MATCHES "Red Hat" OR ${os_name} MATCHES "CentOS") AND ( ${os_version} MATCHES "7" ) )
	add_compile_options(-D RHEL_CENTOS_7)
endif()

# Set plugin type (south, north, filter)
set(PLUGIN_TYPE "notificationDelivery")

# Generation version header file
set_source_files_properties(version.h PROPERTIES GENERATED TRUE)
add_custom_command(
  OUTPUT version.h
  DEPENDS ${CMAKE_SOURCE_DIR}/VERSION
  COMMAND ${CMAKE_SOURCE_DIR}/mkversion ${CMAKE_SOURCE_DIR}
  COMMENT "Generating version header"
  VERBATIM
)
include_directories(${CMAKE_BINARY_DIR})

# Add here all needed Fledge libraries as list
set(NEEDED_FLEDGE_LIBS common-lib services-common-lib plugins-common-lib)

# Find source files
file(GLOB SOURCES operation.cpp plugin.cpp)

# Find Fledge includes and libs, by including FindFledge.cmak file
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR})
find_package(Fledge)
# If errors: make clean and remove Makefile
if (NOT FLEDGE_FOUND)
	if (EXISTS "${CMAKE_BINARY_DIR}/Makefile")
		execute_process(COMMAND make clean WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
		file(REMOVE "${CMAKE_BINARY_DIR}/Makefile")
	endif()
	# Stop the build process
	message(FATAL_ERROR "Fledge plugin '${PROJECT_NAME}' build error.")
endif()
# On success, FLEDGE_INCLUDE_DIRS and FLEDGE_LIB_DIRS variables are set 

# Add ./include
include_directories(include)

# Add Fledge include dir(s)
include_directories(${FLEDGE_INCLUDE_DIRS})

# Add Notification server includes from NOTIFICATION_SERVICE_INCLUDE_DIRS env variable
if (NOT DEFINED ENV{NOTIFICATION_SERVICE_INCLUDE_DIRS})
        # Stop the build process
        message(FATAL_ERROR "Fledge plugin '${PROJECT_NAME}' build error. "
                "Notification server includes dir not set. Use NOTIFICATION_SERVICE_INCLUDE_DIRS env variable")
else()
        message(STATUS "Notification server includes dir set to $ENV{NOTIFICATION_SERVICE_INCLUDE_DIRS}")
endif()

include_directories($ENV{NOTIFICATION_SERVICE_INCLUDE_DIRS})

# Add other include paths this plugin needs
if (FLEDGE_SRC)
	message(STATUS "Using third-party includes " ${FLEDGE_SRC}/C/thirdparty/Simple-Web-Server)
	include_directories(${FLEDGE_SRC}/C/thirdparty/Simple-Web-Server)
	include_directories(${FLEDGE_SRC}/C/plugins/common/include)
else()
	include_directories(${FLEDGE_INCLUDE_DIRS}/Simple-Web-Server)
endif()

# Add Fledge lib path
link_directories(${FLEDGE_LIB_DIRS})

# Create shared library
add_library(${PROJECT_NAME} SHARED ${SOURCES} version.h)

# Add Fledge library names
target_link_libraries(${PROJECT_NAME} ${NEEDED_FLEDGE_LIBS})
# Add additional libraries

# Set the build version 
set_target_properties(${PROJECT_NAME} PROPERTIES SOVERSION 1)

set(FLEDGE_INSTALL "" CACHE INTERNAL "")
# Install library
if (FLEDGE_INSTALL)
	message(STATUS "Installing ${PROJECT_NAME} in ${FLEDGE_INSTALL}/plugins/${PLUGIN_TYPE}/${PROJECT_NAME}")
	install(TARGETS ${PROJECT_NAME} DESTINATION ${FLEDGE_INSTALL}/plugins/${PLUGIN_TYPE}/${PROJECT_NAME})
endif()
