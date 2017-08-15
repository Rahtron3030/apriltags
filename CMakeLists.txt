cmake_minimum_required(VERSION 2.8.3)
project(apriltags)

# Set up the ROS Catkin package settings.
find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  std_msgs
  sensor_msgs
  image_transport
  roscpp
  cmake_modules
  message_generation
  geometry_msgs
  apriltags_swatbotics
)
find_package(OpenCV REQUIRED)
find_package(Eigen REQUIRED)

# apriltag cpp
#apriltagscpp_INCLUDE_DIR=${CMAKE_CURRENT_SOURCE_DIR}/..
#apriltagscpp_LIBRARY:${CMAKE_SOURCE_DIR}/apriltags-cpp
#include_directories(${CMAKE_SOURCE_DIR}/apriltags-cpp/src)
#link_directories(${CMAKE_SOURCE_DIR}/apriltags-cpp/build)
#set(CMAKE_MODULE_PATH "/home/rahtron/projectionomy/projection/catkin_ws/src/apriltags/apriltags-cpp;${CMAKE_MODULE_PATH}")
#set(apriltagscpp_LIBRARY "/home/rahtron/projectionomy/projection/catkin_ws/src/apriltags/apriltags-cpp")
#set(apriltagscpp_INCLUDE "/home/rahtron/projectionomy/projection/catkin_ws/src/apriltags/apriltags-cpp/src")

#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I/home/rahtron/projectionomy/projection/catkin_ws/src/apriltags/apriltags-cpp -I/home/rahtron/projectionomy/projection/catkin_ws/src/apriltags/apriltags-cpp/src")
#LINK_DIRECTORIES(${CMAKE_SOURCE_DIR}/apriltags-cpp/build)
#TARGET_LINK_LIBRARIES(apriltagscpp_LIBRARY ${CMAKE_SOURCE_DIR}/apriltags-cpp/build/libapriltags.a)

#find_package(apriltagscpp REQUIRED)
#set(apriltagscpp_DIR ${CMAKE_CURRENT_SOURCE_DIR}/apriltags-cpp)
#add_subdirectory(${apriltagscpp_DIR}/build)
#include_directories(${apriltagscpp_DIR}/src)
#set(CMAKE_MODULE_PATH ${apriltagscpp_DIR})
#add_library(apriltagscpp_LIBRARY
#LINK_DIRECTORIES(${CMAKE_BINARY_DIR}/res)


# Import the yaml-cpp libraries.
include(FindPkgConfig)
pkg_check_modules(Yaml REQUIRED yaml-cpp)

add_message_files(DIRECTORY msg FILES
    AprilTagDetection.msg
    AprilTagDetections.msg
)

generate_messages(DEPENDENCIES
    std_msgs
    geometry_msgs
)

# Set up the ROS Catkin package settings
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS cv_bridge
                 std_msgs
                 sensor_msgs
                 image_transport
                 roscpp
                 geometry_msgs
)

# CGAL requires that -frounding-math be set.
add_definitions(-frounding-math)

include_directories(
    include/
    ${catkin_INCLUDE_DIRS}
    ${Eigen_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
    ${Yaml_INCLUDE_DIRS}
    ${apriltagscpp_INCLUDE_DIR}/apriltagscpp
)

add_executable(apriltags src/apriltags.cpp)
target_link_libraries(apriltags ${catkin_LIBRARIES}
                                ${Eigen_LIBRARIES}
                                ${OpenCV_LIBRARIES}
                                ${Yaml_LIBRARIES}
                                ${apriltagscpp_LIBRARY})
add_dependencies(apriltags ${PROJECT_NAME}_gencpp)

install(TARGETS apriltags
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

#install(FILES ${apriltagscpp_LIBRARY}
#  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#)
