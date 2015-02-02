IF(NOT BIICODE)
cmake_minimum_required(VERSION 2.8.11)

#-----------------------------
# Project Settings
#-----------------------------

SET(PROJECTNAME "OpenCV Examples")
PROJECT(${PROJECTNAME} C CXX)
SET( ${PROJECT_NAME}_MAJOR_VERSION 1 )
SET( ${PROJECT_NAME}_MINOR_VERSION 0 )
SET( ${PROJECT_NAME}_PATCH_LEVEL 0 )
SET( ${PROJECT_NAME}_VERSION_STRING
        ${${PROJECT_NAME}_MAJOR_VERSION}.${${PROJECT_NAME}_MINOR_VERSION}.${${PROJECT_NAME}_PATCH_LEVEL})

#General CMake/Project Settings
INCLUDE(${CMAKE_SOURCE_DIR}/CMakeModules/project_props.cmake)
#Dependencies Settings
INCLUDE(${CMAKE_SOURCE_DIR}/CMakeModules/deps.cmake)
#Macro Definitions
INCLUDE(${CMAKE_SOURCE_DIR}/CMakeModules/macros.cmake)

#-----------------------------
# Subproject Includes
#-----------------------------
SET(PROJECT_PREFIX_NAME "cv")
ADD_SUBDIRECTORY(basic_operations)
ADD_SUBDIRECTORY(feature_extraction)
ADD_SUBDIRECTORY(image_processing)
ADD_SUBDIRECTORY(object_detection)
if(WIN32)
ADD_SUBDIRECTORY(deploy_all)	#bundle for Windows
endif(WIN32)

#-----------------------------
# Copies the test dataset into install folder
#-----------------------------
file(COPY
    ${CMAKE_SOURCE_DIR}/test_data/
    DESTINATION ${CMAKE_INSTALL_PREFIX}/bin/test_data)

ELSE()
	file(READ ${CMAKE_CURRENT_SOURCE_DIR}/biicode.conf BIICODE_CONF)
	string(FIND ${BIICODE_CONF}  "opencv(beta)" pos1)
	if(NOT pos1 EQUAL -1)
		add_definitions(-DOPENCV_NEW=1)
	endif()
	file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/test_data/
	     DESTINATION ${CMAKE_SOURCE_DIR}/../bin/test_data)

	INIT_BIICODE_BLOCK()
	ADD_BIICODE_TARGETS()
	foreach(EXECUTABLE ${BII_BLOCK_EXES})
		SET_TARGET_PROPERTIES(${BII_${EXECUTABLE}_TARGET} PROPERTIES OUTPUT_NAME ${EXECUTABLE})
	endforeach()
ENDIF()
