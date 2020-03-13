cmake_minimum_required(VERSION 2.8.3)
project(viso2_ros)

find_package(catkin REQUIRED COMPONENTS
  libviso2
  roscpp
  sensor_msgs
  nav_msgs
  message_filters
  image_transport
  cv_bridge
  image_geometry
  tf
  pcl_ros
  std_srvs
  message_generation
  dynamic_reconfigure
)

find_package(OpenCV REQUIRED)
find_package(PCL REQUIRED)

add_message_files(DIRECTORY msg FILES VisoInfo.msg)

generate_messages(
  DEPENDENCIES
    std_msgs
)

generate_dynamic_reconfigure_options(
  cfg/dynamic_params.cfg
)

catkin_package()

IF (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")
  MESSAGE(STATUS "Detected ${CMAKE_SYSTEM_PROCESSOR} architecture")
  MESSAGE(STATUS "ARM CROSS COMPILATION - [ACTIVATED]")
  SET(CMAKE_SYSTEM_PROCESSOR arm)
  SET(CMAKE_CXX_FLAGS -mfpu=neon)
ELSE (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")
  add_definitions(-msse3)
ENDIF (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l")

include_directories(src ${libviso2_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})

add_executable(stereo_odometer
  src/stereo_odometer.cpp)
add_dependencies(stereo_odometer ${catkin_EXPORTED_TARGETS})
add_dependencies(stereo_odometer ${PROJECT_NAME}_gencpp)
add_dependencies(stereo_odometer ${PROJECT_NAME}_gencfg)

add_executable(mono_odometer
  src/mono_odometer.cpp)
add_dependencies(mono_odometer ${catkin_EXPORTED_TARGETS})
add_dependencies(mono_odometer ${PROJECT_NAME}_gencpp)

add_executable(mono_odometer_omnidirectional
  src/mono_odometer_omnidirectional.cpp)
add_dependencies(mono_odometer_omnidirectional ${catkin_EXPORTED_TARGETS})
add_dependencies(mono_odometer_omnidirectional ${PROJECT_NAME}_gencpp)

target_link_libraries(stereo_odometer ${OpenCV_LIBRARIES} ${catkin_LIBRARIES})
find_package(Boost REQUIRED COMPONENTS signals thread)
include_directories(${Boost_INCLUDE_DIRS})
target_link_libraries(stereo_odometer ${Boost_LIBRARIES} ${catkin_LIBRARIES})
target_link_libraries(mono_odometer ${OpenCV_LIBRARIES} ${catkin_LIBRARIES})
target_link_libraries(mono_odometer_omnidirectional ${OpenCV_LIBRARIES} ${catkin_LIBRARIES})
