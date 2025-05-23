cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD 17)
SET(CMAKE_OSX_DEPLOYMENT_TARGET 15.2)

project("daapi_c" LANGUAGES C)

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
  set(LIB_PATH debug)
else()
  set(LIB_PATH release)
endif()

set(PROJECT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/../..)

find_package(cJSON REQUIRED)

function(add_example TARGET_NAME CPP_FILE)
  # source files for the client app
  set(SRC_FILE
          ${CMAKE_CURRENT_SOURCE_DIR}/${CPP_FILE}
          ${CMAKE_CURRENT_SOURCE_DIR}/src/config.c
  )

  add_executable(${TARGET_NAME} ${SRC_FILE})

  # add libraries
  target_link_libraries(${TARGET_NAME}
          PUBLIC
          "-L${PROJECT_ROOT}/target/${LIB_PATH} -Wl,-rpath ${PROJECT_ROOT}/target/${LIB_PATH}" direct_access_capi
  )

  target_link_libraries(${TARGET_NAME} PRIVATE cjson::cjson)

  if (APPLE)
    target_link_libraries(${TARGET_NAME} PUBLIC "-framework Security")
    target_link_libraries(${TARGET_NAME} PUBLIC "-framework CoreFoundation")
    target_link_libraries(${TARGET_NAME} PUBLIC "-framework SystemConfiguration")
  elseif(UNIX)
    target_link_libraries(${TARGET_NAME} PUBLIC "-lssl")
    target_link_libraries(${TARGET_NAME} PUBLIC "-lcrypto")
    target_link_libraries(${TARGET_NAME} PUBLIC "-ldl")
    target_link_libraries(${TARGET_NAME} PUBLIC "-lpthread")
    target_link_libraries(${TARGET_NAME} PUBLIC "-lm")
  endif()

  # path to headerfile
  target_include_directories(${TARGET_NAME}
          PRIVATE
          ${CMAKE_CURRENT_SOURCE_DIR}/..
  )

  install (TARGETS ${TARGET_NAME} DESTINATION .)
endfunction()

add_example(delete_job src/delete_job.c)
add_example(cancel_job src/cancel_job.c)
add_example(list_backends src/list_backends.c)
add_example(list_jobs src/list_jobs.c)
add_example(run_job src/run_job.c)
add_example(run_primitive src/run_primitive.c)
add_example(s3_test src/s3.c)
add_example(uuid_test src/uuid.c)
add_example(version src/version.c)
