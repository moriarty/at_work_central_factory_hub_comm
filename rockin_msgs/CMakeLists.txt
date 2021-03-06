cmake_minimum_required(VERSION 2.8.7)
project(rockin_msgs)

find_package(Protobuf REQUIRED)

include_directories(${PROTOBUF_INCLUDE_DIRS})

set(CFH_ROOT_DIR ${PROJECT_SOURCE_DIR}/../at_work_central_factory_hub)

file(GLOB CFH_PROTOS ${CFH_ROOT_DIR}/rockin/msgs/*.proto)

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS ${CFH_PROTOS})

add_library(rockin_msgs SHARED ${PROTO_SRCS})
target_link_libraries(rockin_msgs ${PROTOBUF_LIBRARIES})

## if: using catkin_make
if(DEFINED _CATKIN_ALL_INCLUDED_)
  find_package(catkin REQUIRED)

  catkin_package(
    LIBRARIES
      rockin_msgs
  )

  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_INCLUDE_DESTINATION}/${PROJECT_NAME} )

  add_custom_target(copy ALL
    DEPENDS ${PROTO_HDRS}
  )

  foreach(File ${PROTO_HDRS})
    add_custom_command(TARGET copy POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy ${File} ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_INCLUDE_DESTINATION}/${PROJECT_NAME} 
    )
  endforeach()

  install(FILES ${PROTO_HDRS}
    DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  )
## else: using cmake
else()
  install(
    TARGETS rockin_msgs
    LIBRARY DESTINATION lib
  )
  install(
    FILES ${PROTO_HDRS}
    DESTINATION include/${PROJECT_NAME}
  )

endif(DEFINED _CATKIN_ALL_INCLUDED_)

