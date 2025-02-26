cmake_minimum_required (VERSION 3.13)
project (final_pipeline)

#change to your raspicam build directory
#list(APPEND CMAKE_MODULE_PATH "/home/pi/Documents/EEE190/raspicam-0.1.6/build")

#find_package(raspicam REQUIRED)
find_package(PkgConfig REQUIRED)
find_package(OpenCV REQUIRED)

#IF  ( OpenCV_FOUND AND raspicam_CV_FOUND)

IF  (OpenCV_FOUND)
pkg_check_modules(OPENCV4 REQUIRED IMPORTED_TARGET opencv4)

#change to the path of your RaspberryPi TFLite C++ static library
set(RPI_TFLITE_LIBPATH "/home/pi/Documents/EEE190/tensorflow/tensorflow/lite/tools/make/gen/linux_aarch64/lib/libtensorflow-lite.a")

#change to the path of your TFlite repo local directory
set(TFLITE_INCLUDEPATH_1 "/home/pi/Documents/EEE190/tensorflow/tensorflow/lite/tools/make/downloads/flatbuffers/include")
set(TFLITE_INCLUDEPATH_2 "/home/pi/Documents/EEE190/tensorflow/")

MESSAGE(STATUS "COMPILING PIPELINE")

add_executable (liveSSD_pipeline liveSSD_pipeline.cpp ${TFLITE_INCLUDEPATH_2}/tensorflow/lite/interpreter.h ${TFLITE_INCLUDEPATH_2}/tensorflow/lite/kernels/register.h ${TFLITE_INCLUDEPATH_2}/tensorflow/lite/model.h ${TFLITE_INCLUDEPATH_2}/tensorflow/lite/optional_debug_tools.h)
target_include_directories(liveSSD_pipeline PUBLIC ${TFLITE_INCLUDEPATH_1} ${TFLITE_INCLUDEPATH_2})

#The below line doesn't work so use legacy command set(CMAKE_CXX_FLAGS) for now
#target_compile_options(liveSSD_pipeline PUBLIC -Wall -Wextra -pedantic -pthread)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic -pthread -ggdb ${OPENCV4_CFLAGS}")

target_link_libraries (liveSSD_pipeline ${RPI_TFLITE_LIBPATH} PkgConfig::OPENCV4)

ELSE()
MESSAGE(FATAL_ERROR "OPENCV NOT FOUND IN YOUR SYSTEM")
ENDIF()
