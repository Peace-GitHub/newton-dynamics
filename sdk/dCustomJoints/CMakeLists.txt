# Copyright (c) <2014-2017> <Newton Game Dynamics>
#
# This software is provided 'as-is', without any express or implied
# warranty. In no event will the authors be held liable for any damages
# arising from the use of this software.
#
# Permission is granted to anyone to use this software for any purpose,
# including commercial applications, and to alter it and redistribute it
# freely.

cmake_minimum_required(VERSION 3.4.0)

set (projectName "dCustomJoints")
message (${projectName})

#source and header files
file(GLOB CPP_SOURCE *.h *.cpp)
file(GLOB HEADERS *.h)

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/" FILES ${CPP_SOURCE})

if(NEWTON_BUILD_SHARED_LIBS)
	add_library(${projectName} SHARED ${CPP_SOURCE})
	add_definitions(-D_DSCENE_DLL)
	add_definitions(-D_DCONTAINERS_DLL)
	add_definitions(-D_CUSTOM_JOINTS_BUILD_DLL)
else()
	add_library(${projectName} STATIC ${CPP_SOURCE})
	add_definitions(-D_NEWTON_STATIC_LIB)
	add_definitions(-D_CUSTOM_JOINTS_STATIC_LIB)
endif()

target_include_directories(${projectName} PUBLIC .)
target_link_libraries (${projectName} newton dMath dContainers dgCore)
if (NEWTON_BUILD_PROFILER)
    target_link_libraries (${projectName} dProfiler)
endif()

if (MSVC)
    if(CMAKE_VS_MSBUILD_COMMAND OR CMAKE_VS_DEVENV_COMMAND)
        set_target_properties(${projectName} PROPERTIES COMPILE_FLAGS "/YudCustomJointLibraryStdAfx.h")
        set_source_files_properties(dCustomJointLibraryStdAfx.cpp PROPERTIES COMPILE_FLAGS "/YcdCustomJointLibraryStdAfx.h")
    endif()

	if (NEWTON_BUILD_SANDBOX_DEMOS)
		add_custom_command(
			TARGET ${projectName} POST_BUILD
			COMMAND ${CMAKE_COMMAND}
			ARGS -E copy $<TARGET_FILE:${projectName}> ${PROJECT_BINARY_DIR}/applications/demosSandbox/${CMAKE_CFG_INTDIR}/$<TARGET_FILE_NAME:${projectName}>)
	endif ()
endif(MSVC)

install(TARGETS ${projectName} 
		LIBRARY DESTINATION lib
		ARCHIVE DESTINATION lib
		RUNTIME DESTINATION bin)

install(FILES ${HEADERS} DESTINATION include/${projectName})
