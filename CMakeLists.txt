cmake_minimum_required(VERSION 2.8.11)
project(external_protobuf)

include(${PROJECT_SOURCE_DIR}/cmake/protogen.cmake)

set(PBUF_DIR ${PROJECT_BINARY_DIR}/protobuf)

include(ExternalProject)
ExternalProject_Add(protobuf
    PREFIX ${PROTOBUF_DIR}
    GIT_REPOSITORY https://github.com/google/protobuf.git
    GIT_TAG v3.4.1
    UPDATE_DISCONNECTED 1
    BUILD_IN_SOURCE 1
    CONFIGURE_COMMAND ./autogen.sh COMMAND ./configure --prefix=${PBUF_DIR}
)

set(PBUF_INCLUDE_DIR ${PBUF_DIR}/include)
set(PBUF_LIBRARY ${PBUF_DIR}/lib/libprotobuf.so)
set(PBUF_PROTOC ${PBUF_DIR}/bin/protoc)

file(GLOB PBUF_FILES "${PROJECT_SOURCE_DIR}/msg/*.proto")
custom_protobuf_generate_cpp(PBUF_SRCS PBUF_HDRS ${PBUF_FILES})

include_directories(
    ${PROJECT_BINARY_DIR}
    ${PBUF_INCLUDE_DIR}
)

add_executable(${PROJECT_NAME} main.cpp ${PBUF_SRCS} ${PBUF_HDRS})
add_dependencies(${PROJECT_NAME} protobuf)
target_link_libraries(${PROJECT_NAME}
    ${PBUF_LIBRARY}
)