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
link_directories(/tmp/lib)

add_executable(main ${SRC_FILES})


target_link_libraries(main ${SDL2_LIBRARIES} -lGL -lm -pthread -ldl -lprotobuf)

set(CXXFLAGS "-D_REENTRANT")

include_directories(${SDL2_INCLUDE_DIRS} gl2/include eigen ../)
