
cmake_minimum_required(VERSION 2.8)
project(nimbro_cam_transport)

find_package(catkin REQUIRED COMPONENTS
	image_transport
	roscpp
	cv_bridge
)

catkin_package()

include_directories(${catkin_INCLUDE_DIRS})

find_library(x264_LIBRARY x264)

if(NOT x264_LIBRARY)
	message(WARNING "x264 library not found, not building senders...")
endif()

#find_library(LIBAVUTIL_LIBRARY avutil HINTS /opt/ffmpeg/lib)
#find_library(LIBAVCODEC_LIBRARY avcodec HINTS /opt/ffmpeg/lib)
#find_library(LIBSWSCALE_LIBRARY swscale HINTS /opt/ffmpeg/lib)

find_path(AVCODEC_INCLUDE_DIR libavcodec/avcodec.h)
find_library(AVCODEC_LIBRARY avcodec)

find_path(AVFORMAT_INCLUDE_DIR libavformat/avformat.h)
find_library(AVFORMAT_LIBRARY avformat)

find_path(AVUTIL_INCLUDE_DIR libavutil/avutil.h)
find_library(AVUTIL_LIBRARY avutil)

find_path(AVDEVICE_INCLUDE_DIR libavdevice/avdevice.h)
find_library(AVDEVICE_LIBRARY avdevice)

find_path(SWSCALE_INCLUDE_DIR libswscale/swscale.h)
find_library(SWSCALE_LIBRARY swscale)


if(x264_LIBRARY)
	add_executable(cam_sender
		src/sender.cpp
		src/rgb_to_yuv420.cpp
	)
	target_link_libraries(cam_sender
		${catkin_LIBRARIES}
		${x264_LIBRARY}
	)
endif()

if(AVCODEC_LIBRARY)
	add_executable(cam_receiver
		src/receiver.cpp
	)
	target_link_libraries(cam_receiver
		${catkin_LIBRARIES}
		${AVUTIL_LIBRARY}
		${AVCODEC_LIBRARY}
		${SWSCALE_LIBRARY}
	)
endif()

#install
install(TARGETS cam_sender cam_receiver
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

