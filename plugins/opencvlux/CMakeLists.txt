if(opencvlux_plugin)

find_package(OpenCV REQUIRED)

if(OpenCV_LIBS)
	 message(STATUS "opencv found")
else(OpenCV_LIBS)
	 message(FATAL_ERROR "opencv missing.  please install opencv")
endif(OpenCV_LIBS)

#find opencv ocl headers:
INCLUDE (CheckLibraryExists)
CHECK_LIBRARY_EXISTS(cl clCreateImage /usr/lib/beignet ocl)

if(ocl)
	message(STATUS "found opencv ocl headers. enabling opencl support. ${ocl}")
	add_definitions(-DOPENCL)
else(ocl)
	message(STATUS "no opencl support")
endif(ocl)

find_path(cuda gpu.hpp PATH_SUFFIXES opencv/gpu opencv2/gpu DOC "opencv cuda headers")

if(cuda)
	message(STATUS "found opencv cuda headers.  enabling cuda support. ${cuda}")
	add_definitions(-DCUDA)
else(cuda)
	message(STATUS "no opencv cuda headers found.  no cuda support")
endif(cuda)

include_directories(${CMAKE_SOURCE_DIR}/lib ${include_dirs} ${OpenCV_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS})

set(opencvluxplugin_headers opencvluxplugin.h)
set(opencvluxplugin_sources opencvluxplugin.cpp)

add_library(opencvluxplugin MODULE ${opencvluxplugin_sources} ${opencvluxplugin_headers_moc})
set_target_properties(opencvluxplugin PROPERTIES PREFIX "")
target_link_libraries(opencvluxplugin amb -L${CMAKE_CURRENT_BINARY_DIR}/lib ${link_libraries} ${OpenCV_LIBS} ${Boost_LIBRARIES})

install(TARGETS opencvluxplugin LIBRARY DESTINATION lib${LIB_SUFFIX}/automotive-message-broker)

endif(opencvlux_plugin)
