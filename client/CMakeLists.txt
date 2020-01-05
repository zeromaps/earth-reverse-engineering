cmake_minimum_required(VERSION 3.5)

project(earth_client)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_COMPILER clang++)
file(GLOB PROTOBUF_DEFINITION_FILES "${PROJECT_SOURCE_DIR}/../proto/*.proto")

# Compile proto files
message(STATUS "PROTOBUF_DEFINITION_FILES: ${PROTOBUF_DEFINITION_FILES}")
set(PROTOBUF_INPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/../proto/")
set(PROTOBUF_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/proto/")
foreach(file ${PROTOBUF_DEFINITION_FILES})
    set(PROTOBUF_ARGUMENTS --proto_path=${PROTOBUF_INPUT_DIRECTORY} --cpp_out=${PROTOBUF_OUTPUT_DIRECTORY} ${file})
    execute_process(COMMAND protoc ${PROTOBUF_ARGUMENTS}
            WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
            RESULT_VARIABLE PROTOBUF_RESULT
            OUTPUT_VARIABLE PROTOBUF_OUTPUT_VARIABLE)
endforeach()

find_package(sdl2 REQUIRED)

set(SRC_FILES crn/crn.cc main.cpp)

link_directories(/usr/local/lib)

add_executable(main ${SRC_FILES})


target_link_libraries(main ${SDL2_LIBRARIES} -lGL -lm -pthread -ldl -lprotobuf)

set(CXXFLAGS "-D_REENTRANT")

include_directories(${SDL2_INCLUDE_DIRS} gl2/include eigen ../)

#-D_REENTRANT -pthread -I/usr/local/include -I/usr/include/SDL2
#file(GLOB PROTOBUF_MODELS_INCLUDES "Models/Proto/*.pb.cc" "Models/Proto/*.hpp")

##!/bin/sh
#
#if [ "$1" == "emscripten" ]; then
#	/home/wenjun/Downloads/emsdk/emsdk activate 1.38.45
#	source /home/wenjun/Downloads/emsdk/emsdk_env.sh
#	source config_emscripten.sh
#	echo build: emscripten
#	pwd="$(pwd)" && cd .. && $EMSCRIPTEN_PROTOBUF_EXE --cpp_out=client proto/rocktree.proto && cd "$pwd"
#	cd crn && emcc -std=c++14 -c crn.cc -w && cd ..
#        #EMCC_DEBUG=1 
#	emcc -Iinclude main.cpp -O2 -std=c++14 -I. -I./eigen/ \
#		-I$EMSCRIPTEN_PROTOBUF_SRC $EMSCRIPTEN_PROTOBUF_LIB -I/usr/local/include -L/usr/local/lib -lprotobuf crn/crn.o \
#		-s USE_SDL=2 -s FETCH=1 -s TOTAL_MEMORY=1073741824 -s USE_PTHREADS=1 -s PTHREAD_POOL_SIZE=4 \
#		-o main.html	
#else
#	echo build: native
#	pwd="$(pwd)" && cd .. && ./makeproto.sh && cd "$pwd"
#	cd crn && g++ -std=c++14 -c crn.cc -w && cd ..
#
#	CFLAGS="--std=c++14 -g -I. `pkg-config --cflags sdl2 protobuf` -I./eigen/"
#	LDFLAGS="`pkg-config --libs sdl2 protobuf` crn/crn.o -Wl,-rpath,/usr/local/lib"
#	if [ `uname` = "Darwin" ]; then		
#		CFLAGS="$CFLAGS `pkg-config --cflags glew`"
#		LDFLAGS="$LDFLAGS `pkg-config --static --libs glew` -framework OpenGL"
#		echo "$CFLAGS"
#		echo "$LDFLAGS"
#	else
#		CFLAGS="$CFLAGS -Igl2/include"
#		LDFLAGS="$LDFLAGS -lGL -lm -ldl"
#	fi
#	#c++ $CFLAGS main.cpp $LDFLAGS -o main
#	clang++ $CFLAGS main.cpp $LDFLAGS -o main
#fi
