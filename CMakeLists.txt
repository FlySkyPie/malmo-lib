# ------------------------------------------------------------------------------------------------
# Copyright (c) 2016 Microsoft Corporation
# Copyright (c) 2021 Wei Ji
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy of this software and
# associated documentation files (the "Software"), to deal in the Software without restriction,
# including without limitation the rights to use, copy, modify, merge, publish, distribute,
# sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all copies or
# substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT
# NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM,
# DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
# ------------------------------------------------------------------------------------------------

cmake_minimum_required(VERSION 3.0)
include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.312.tar.gz"
    SHA1 "42311324f6b859983d833cffc2151d0b86b8bea4"
    LOCAL
)

project(Makmo)

hunter_add_package(Boost COMPONENTS chrono date_time filesystem iostreams program_options regex system thread)
hunter_add_package(ZLIB)

set( MALMO_VERSION 0.37.0 )

find_package(ZLIB)
find_package( Boost 1.65.1 COMPONENTS chrono date_time filesystem iostreams program_options regex system thread REQUIRED )

set( SOURCES
   src/AgentHost.cpp
   src/ArgumentParser.cpp
   src/ErrorCodeSync.cpp
   src/ClientConnection.cpp
   src/ClientInfo.cpp
   src/ClientPool.cpp
   src/Logger.cpp
   src/FindSchemaFile.cpp
   src/RewardXML.cpp
   src/MissionInitXML.cpp
   src/MissionEndedXML.cpp
   src/MissionInitSpec.cpp
   src/MissionRecord.cpp
   src/MissionRecordSpec.cpp
   src/MissionSpec.cpp
   src/ParameterSet.cpp
   src/StringServer.cpp
   src/TCPClient.cpp
   src/TCPConnection.cpp
   src/TCPServer.cpp
   src/TimestampedReward.cpp
   src/TimestampedString.cpp
   src/TimestampedVideoFrame.cpp
   src/VideoFrameWriter.cpp
   src/BmpFrameWriter.cpp
   src/VideoServer.cpp
   src/WorldState.cpp
)

set( HEADERS
   include/AgentHost.h
   include/ArgumentParser.h
   include/ErrorCodeSync.h
   include/ClientConnection.h
   include/ClientInfo.h
   include/ClientPool.h
   include/Logger.h
   include/FindSchemaFile.h
   include/XMLParseException.h
   include/RewardXML.h
   include/MissionInitXML.h
   include/MissionEndedXML.h
   include/MissionInitSpec.h
   include/MissionRecord.h
   include/MissionRecordSpec.h
   include/MissionSpec.h
   include/ParameterSet.h
   include/StringServer.h
   include/Tarball.hpp
   include/TCPClient.h
   include/TCPConnection.h
   include/TCPServer.h
   include/TimestampedUnsignedCharVector.h
   include/TimestampedReward.h
   include/TimestampedString.h
   include/TimestampedVideoFrame.h
   include/VideoFrameWriter.h
   include/BmpFrameWriter.h
   include/VideoServer.h
   include/WorldState.h
)



if( MSVC )
  set( SOURCES ${SOURCES} src/WindowsFrameWriter.cpp )
  set( HEADERS ${HEADERS} include/WindowsFrameWriter.h )
else()
  set( SOURCES ${SOURCES} src/PosixFrameWriter.cpp )
  set( HEADERS ${HEADERS} include/PosixFrameWriter.h )
endif()

if ( INCLUDE_ALE )
  set( SOURCES ${SOURCES} src/ALEAgentHost.cpp )
  set( HEADERS ${HEADERS} include/ALEAgentHost.h )
endif()

add_definitions( -DMALMO_VERSION=${MALMO_VERSION} )

set( MALMO_INCLUDE_FOLDERS 
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${Boost_INCLUDE_DIR} 
    ${ZLIB_INCLUDE_DIRS}
)
if ( INCLUDE_ALE )
  set( MALMO_INCLUDE_FOLDERS ${MALMO_INCLUDE_FOLDERS} ${ALE_INCLUDE_DIRS} )
endif()

include_directories( ${MALMO_INCLUDE_FOLDERS} )

add_library( Malmo STATIC ${SOURCES} ${HEADERS} )

set( MALMO_LINK_LIBRARIES 
  ${Boost_LIBRARIES} 
  ${ZLIB_LIBRARIES}
)
if( INCLUDE_ALE )
  set( MALMO_LINK_LIBRARIES ${MALMO_LINK_LIBRARIES} ${ALE_LIBRARIES} )
endif()
if( UNIX AND NOT APPLE )
  set( MALMO_LINK_LIBRARIES ${MALMO_LINK_LIBRARIES} rt )
endif()
if( NOT WIN32 )
  set( MALMO_LINK_LIBRARIES ${MALMO_LINK_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT} )
endif()

target_link_libraries( Malmo ${MALMO_LINK_LIBRARIES} )
target_include_directories(Malmo PRIVATE include)