# CMake entry point
cmake_minimum_required (VERSION 3.6)
project (opengltutorials)

find_package(OpenGL REQUIRED)


if( CMAKE_BINARY_DIR STREQUAL CMAKE_SOURCE_DIR )
    message( FATAL_ERROR "Please select another Build Directory ! (and give it a clever name, like bin_Visual2012_64bits/)" )
endif()
if( CMAKE_SOURCE_DIR MATCHES " " )
	message( "Your Source Directory contains spaces. If you experience problems when compiling, this can be the cause." )
endif()
if( CMAKE_BINARY_DIR MATCHES " " )
	message( "Your Build Directory contains spaces. If you experience problems when compiling, this can be the cause." )
endif()



# Compile external dependencies 
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../external ${CMAKE_CURRENT_BINARY_DIR}/external)

# On Visual 2005 and above, this module can set the debug working directory
cmake_policy(SET CMP0026 OLD)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/../external/rpavlik-cmake-modules-fe2273")
include(CreateLaunchers)
include(MSVCMultipleProcessCompile) # /MP

if(INCLUDE_DISTRIB)
	add_subdirectory(distrib)
endif(INCLUDE_DISTRIB)



include_directories(
	#external/AntTweakBar-1.16/include/
	../external/glfw-3.2/include/GLFW/
	../external/glm-0.9.7.1/
	../external/glew-1.13.0/include/
	../external/soil/src/
	../external/soil/lib/
	#../freetype/include/
	# external/assimp-3.0.1270/include/
	# external/bullet-2.81-rev2613/src/
	.
)

set(ALL_LIBS
	${OPENGL_LIBRARY}
	glfw
	GLEW_1130
	${CMAKE_CURRENT_SOURCE_DIR}/../external/soil/lib/SOIL.lib
	#${CMAKE_CURRENT_SOURCE_DIR}/freetype/freetype262.lib
)

add_definitions(
	-DTW_STATIC
	-DTW_NO_LIB_PRAGMA
	-DTW_NO_DIRECT3D
	-DGLEW_STATIC
	-D_CRT_SECURE_NO_WARNINGS
)


add_executable(tutorial
	../common/shader.hpp
	../common/camera.hpp
	lighting.vert
	lighting.frag
	lamp.vert
	lamp.frag
	main.cpp
	
)
target_link_libraries(tutorial
	${ALL_LIBS}
)
# Xcode and Visual working directories
# set_target_properties(test1 PROPERTIES XCODE_ATTRIBUTE_CONFIGURATION_BUILD_DIR "${CMAKE_CURRENT_SOURCE_DIR}/")
create_target_launcher(tutorial WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/")
create_default_target_launcher(tutorial WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/")



SOURCE_GROUP(common REGULAR_EXPRESSION ".*/../common/.*" )
#SOURCE_GROUP(shaders REGULAR_EXPRESSION ".*/.*shader$" )


if (NOT ${CMAKE_GENERATOR} MATCHES "Xcode" )

add_custom_command(
   TARGET tutorial POST_BUILD
   COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/tutorial${CMAKE_EXECUTABLE_SUFFIX}" "${CMAKE_CURRENT_SOURCE_DIR}/"
)

elseif (${CMAKE_GENERATOR} MATCHES "Xcode" )

endif (NOT ${CMAKE_GENERATOR} MATCHES "Xcode" )

