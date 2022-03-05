cmake_minimum_required(VERSION 2.8.3)
project(prometheus_simulator_utils)

find_package(catkin REQUIRED COMPONENTS
  message_generation  
  roscpp
  geometry_msgs
  nav_msgs
  sensor_msgs
  mavros
  std_msgs
  std_srvs
  tf
  tf2_ros
  tf2_eigen
  mavros_msgs
  prometheus_msgs
  pcl_conversions
)

## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system)
find_package(PCL REQUIRED)
find_package(Eigen3 REQUIRED)

catkin_package()

include_directories(
  SYSTEM
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
  ${PCL_INCLUDE_DIRS}
  ../common/include
)

###############################
##      声明可执行cpp文件     ##
###############################
# add_library(Quadrotor_dynamics src/Quadrotor_dynamics.cpp)
add_library(fake_ugv fake_odom/fake_ugv.cpp)
add_library(fake_uav fake_odom/fake_uav.cpp)

add_executable(fake_odom_node fake_odom/fake_odom_node.cpp)
add_dependencies(fake_odom_node prometheus_fake_odom_gencpp)
target_link_libraries(fake_odom_node ${catkin_LIBRARIES})
target_link_libraries(fake_odom_node fake_ugv)
target_link_libraries(fake_odom_node fake_uav)

add_executable (map_generator map_generator/map_generator.cpp )
target_link_libraries(map_generator
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES})

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

## Mark other files for installation (e.g. launch and bag files, etc.)
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
)