cmake_minimum_required(VERSION 2.8.3)
project(libviso2)

find_package(catkin REQUIRED )

IF (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")
  MESSAGE(STATUS "Detected ${CMAKE_SYSTEM_PROCESSOR} architecture")
  MESSAGE(STATUS "ARM CROSS COMPILATION - [ACTIVATED]")
  SET(CMAKE_SYSTEM_PROCESSOR arm)
  SET(CMAKE_CXX_FLAGS -mfpu=neon)
ELSE (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")
  add_definitions(-msse3)
ENDIF (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")

catkin_package(
  INCLUDE_DIRS libviso2/src
  LIBRARIES viso2
)

include_directories(libviso2/src
  ${catkin_INCLUDE_DIRS}
)

add_library(viso2
  libviso2/src/filter.cpp
  libviso2/src/matcher.cpp
  libviso2/src/matrix.cpp
  libviso2/src/reconstruction.cpp
  libviso2/src/triangle.cpp
  libviso2/src/viso.cpp
  libviso2/src/viso_mono.cpp
  libviso2/src/viso_mono_omnidirectional.cpp
  libviso2/src/viso_stereo.cpp)

install(TARGETS viso2
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY ${PROJECT_NAME}/src
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".cpp" EXCLUDE
)

