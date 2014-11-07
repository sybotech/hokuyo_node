find_library(LOG4CXX_LIBRARY log4cxx)
if(NOT LOG4CXX_LIBRARY)
  message(FATAL_ERROR "Couldn't find log4cxx library")
endif()

rosbuild_add_executable(getID getID.cpp)
target_link_libraries(getID libhokuyo ${LOG4CXX_LIBRARY})

rosbuild_add_executable(getFirmwareVersion getFirmwareVersion.cpp)
target_link_libraries(getFirmwareVersion libhokuyo ${LOG4CXX_LIBRARY})

rosbuild_add_executable(timestamp_test timestamp_test.cpp)
target_link_libraries(timestamp_test ${LOG4CXX_LIBRARY})

