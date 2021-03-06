cmake_minimum_required(VERSION 2.6)
project(aws-cpp-sdk-config)

  file(GLOB AWS_CONFIG_HEADERS
    "include/aws/config/*.h"
  )

  file(GLOB AWS_CONFIG_MODEL_HEADERS
    "include/aws/config/model/*.h"
  )

  file(GLOB AWS_CONFIG_SOURCE
    "source/*.cpp"
  )

  file(GLOB AWS_CONFIG_MODEL_SOURCE
    "source/model/*.cpp"
  )

  file(GLOB CONFIG_UNIFIED_HEADERS
    ${AWS_CONFIG_HEADERS}
    ${AWS_CONFIG_MODEL_HEADERS}
  )

  file(GLOB CONFIG_UNITY_SRC
    ${AWS_CONFIG_SOURCE}
    ${AWS_CONFIG_MODEL_SOURCE}
  )

  if(ENABLE_UNITY_BUILD)
    enable_unity_build("CONFIG" CONFIG_UNITY_SRC)
  endif()

  file(GLOB CONFIG_SRC
    ${CONFIG_UNIFIED_HEADERS}
    ${CONFIG_UNITY_SRC}
  )

  if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
      source_group("Header Files\\aws\\config" FILES ${AWS_CONFIG_HEADERS})
      source_group("Header Files\\aws\\config\\model" FILES ${AWS_CONFIG_MODEL_HEADERS})
      source_group("Source Files" FILES ${AWS_CONFIG_SOURCE})
      source_group("Source Files\\model" FILES ${AWS_CONFIG_MODEL_SOURCE})
    endif(MSVC)
  endif()

  set(CONFIG_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
    "${CORE_DIR}/include/"
  )

  include_directories(${CONFIG_INCLUDES})

  if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    add_definitions("-DAWS_CONFIGSERVICE_EXPORTS")
  endif()

  add_library(aws-cpp-sdk-config ${LIBTYPE} ${CONFIG_SRC})

  target_include_directories(aws-cpp-sdk-config PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)
  
  target_link_libraries(aws-cpp-sdk-config aws-cpp-sdk-core)

  if(SIMPLE_INSTALL)
      install (TARGETS aws-cpp-sdk-config
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}
               LIBRARY DESTINATION lib
               RUNTIME DESTINATION bin)
  else()
    if(PLATFORM_CUSTOM)
      install_custom_library(aws-cpp-sdk-config)
    else()
      install (TARGETS aws-cpp-sdk-config
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               LIBRARY DESTINATION lib/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               RUNTIME DESTINATION bin/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME})
    endif()
  endif()

  install (FILES ${AWS_CONFIG_HEADERS} DESTINATION include/aws/config)
  install (FILES ${AWS_CONFIG_MODEL_HEADERS} DESTINATION include/aws/config/model)

  if(MSVC)
    install (FILES nuget/aws-cpp-sdk-config.autopkg DESTINATION nuget)
  endif()



