cmake_minimum_required(VERSION 2.8.3)
project(hokuyo_node)

find_package(Boost REQUIRED COMPONENTS system thread)

# Utilities that use log4cxx - getID and getFirmwareVersion
find_library(LOG4CXX_LIBRARY log4cxx)
if(NOT LOG4CXX_LIBRARY)
  message(FATAL_ERROR "Couldn't find log4cxx library")
endif()

# Load catkin and all dependencies required for this package
find_package(catkin REQUIRED
  message_generation
  diagnostic_updater
  dynamic_reconfigure
  rosconsole
  roscpp
  self_test
  sensor_msgs
)

include_directories(${Boost_INCLUDE_DIRS})
include_directories(include ${catkin_INCLUDE_DIRS})

add_message_files(FILES
  ConfigString.msg
  ConfigValue.msg
  SensorLevels.msg
)

generate_messages(DEPENDENCIES std_msgs sensor_msgs)

# Dynamic reconfigure support
generate_dynamic_reconfigure_options(cfg/Hokuyo.cfg)

# Export with catkin_package
catkin_package(
    INCLUDE_DIRS include
    CATKIN_DEPENDS rosconsole sensor_msgs
    LIBRARIES libhokuyo
)

# Hokuyo library
add_library(libhokuyo src/hokuyo.cpp)
target_link_libraries(libhokuyo ${rosconsole_LIBRARIES})

# hokuyo_node
add_executable(hokuyo_node src/hokuyo_node.cpp)
add_dependencies(hokuyo_node ${PROJECT_NAME}_gencfg)
add_dependencies(hokuyo_node ${PROJECT_NAME}_gencpp)
target_link_libraries(hokuyo_node
  libhokuyo
  ${Boost_LIBRARIES}
  ${catkin_LIBRARIES}
)

#add_dependencies(hokuyo_node ${${PROJECT_NAME}_EXPORTED_TARGETS})

add_executable(getID src/getID.cpp)
target_link_libraries(getID libhokuyo ${LOG4CXX_LIBRARY} ${catkin_LIBRARIES})

add_executable(getFirmwareVersion src/getFirmwareVersion.cpp)
target_link_libraries(getFirmwareVersion
  libhokuyo
  ${LOG4CXX_LIBRARY}
  ${catkin_LIBRARIES}
)

# Install targets
install(TARGETS libhokuyo DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(TARGETS hokuyo_node getID getFirmwareVersion
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(FILES test/TODO test/unplug_test.launch COPYING.lib hokuyo_test.launch hokuyo_test.vcg hokuyo_test_intensity.launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
