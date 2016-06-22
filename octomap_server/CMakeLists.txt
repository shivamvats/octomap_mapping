# Catkin User Guide: http://www.ros.org/doc/groovy/api/catkin/html/user_guide/user_guide.html
# Catkin CMake Standard: http://www.ros.org/doc/groovy/api/catkin/html/user_guide/standards.html
cmake_minimum_required(VERSION 2.8.3)
project(octomap_server)
# Load catkin and all dependencies required for this package
# TODO: remove all from COMPONENTS that are not catkin packages.
find_package(catkin REQUIRED COMPONENTS visualization_msgs sensor_msgs
    pcl_ros nav_msgs arm_navigation_msgs std_msgs std_srvs octomap_ros
    dynamic_reconfigure octomap_msgs dynamic_reconfigure nodelet
    pcl_conversions)

generate_dynamic_reconfigure_options(${PROJECT_SOURCE_DIR}/cfg/OctomapServer.cfg)

find_package(octomap REQUIRED)

find_package(PCL REQUIRED)

catkin_package(
    DEPENDS visualization_msgs sensor_msgs pcl_ros nav_msgs arm_navigation_msgs
    std_msgs std_srvs octomap_ros dynamic_reconfigure octomap octomap_msgs
    nodelet pcl_conversions
    CATKIN_DEPENDS # TODO
    INCLUDE_DIRS # TODO include
    LIBRARIES # TODO
)

include_directories(${PCL_INCLUDE_DIRS} include cfg/cpp)
include_directories(${catkin_INCLUDE_DIRS})

add_definitions(-DOCTOMAP_NODEBUGOUT)

link_libraries(
  ${PROJECT_NAME}
  ${PCL_COMMON_LIBRARIES}
  ${OCTOMAP_LIBRARIES}
  ${nodelet_LIBRARIES}
)

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#common commands for building c++ executables and libraries
add_library(${PROJECT_NAME} src/OctomapServer.cpp src/OctomapServerMultilayer.cpp src/TrackingOctomapServer.cpp)
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${PCL_LIBRARIES}
    ${OCTOMAP_LIBRARIES})
add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}_gencfg})

add_executable(octomap_server_node src/octomap_server_node.cpp)
target_link_libraries(octomap_server_node ${PROJECT_NAME} ${OCTOMAP_LIBRARIES})

add_executable(octomap_server_static src/octomap_server_static.cpp)
target_link_libraries(octomap_server_static ${PROJECT_NAME} ${OCTOMAP_LIBRARIES})

add_executable(octomap_server_multilayer src/octomap_server_multilayer.cpp)
target_link_libraries(octomap_server_multilayer ${PROJECT_NAME} ${OCTOMAP_LIBRARIES})

add_executable(octomap_saver src/octomap_saver.cpp)
target_link_libraries(octomap_saver ${PROJECT_NAME} ${OCTOMAP_LIBRARIES})

add_executable(octomap_tracking_server_node src/octomap_tracking_server_node.cpp)
target_link_libraries(octomap_tracking_server_node ${PROJECT_NAME} ${OCTOMAP_LIBRARIES})

# Nodelet
add_library(octomap_server_nodelet src/octomap_server_nodelet.cpp)
target_link_libraries(octomap_server_nodelet ${PROJECT_NAME})
