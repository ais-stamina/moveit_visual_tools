cmake_minimum_required(VERSION 2.8.3)
project(moveit_visual_tools)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-address")
set (CMAKE_MODULE_PATH /opt/ros/indigo/share/cmake_modules/cmake/Modules/ ${CMAKE_MODULE_PATH})
MESSAGE(STATUS "CMAKE_MODULE_PATH: " ${CMAKE_MODULE_PATH}) 
include_directories(${catkin_INCLUDE_DIRS})
find_package(Eigen REQUIRED )
find_package(Boost REQUIRED thread system)

# Load catkin and all dependencies required for this package
find_package(catkin REQUIRED COMPONENTS
  eigen_conversions 
  geometry_msgs
  moveit_ros_robot_interaction 
  moveit_core
  roscpp
  tf_conversions
  visualization_msgs
  graph_msgs
  std_msgs
  trajectory_msgs
)

include_directories(${EIGEN_INCLUDE_DIRS} ${graph_msgs_INCLUDE_DIRS})
# Catkin
catkin_package(
  LIBRARIES
    ${PROJECT_NAME}
  CATKIN_DEPENDS
    moveit_ros_robot_interaction
    moveit_core
    geometry_msgs
    visualization_msgs 
    graph_msgs
    std_msgs
    moveit_msgs
    trajectory_msgs
  INCLUDE_DIRS include
)

# Enable gdb to show line numbers
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -g -W -Wall -Wno-unused-parameter -fno-strict-aliasing")
#set(CMAKE_BUILD_TYPE Debug)

## Build 

include_directories(
  include 
  ${catkin_INCLUDE_DIRS}
)

# Visualization Tools Library
add_library(${PROJECT_NAME}
  src/visual_tools.cpp
)
target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES} ${Boost_LIBRARIES}
)
add_dependencies(${PROJECT_NAME}
		graph_msgs_gen_cpp
)
# Test executable
add_executable(visual_tools_test src/visual_tools_test.cpp)
target_link_libraries(visual_tools_test 
  ${catkin_LIBRARIES} ${PROJECT_NAME}
)
add_dependencies(visual_tools_test
                graph_msgs_gen_cpp
)

# Install
install(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

# Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)
