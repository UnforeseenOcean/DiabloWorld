cmake_minimum_required(VERSION 2.6)

set(APP_NAME MyGame)
project (${APP_NAME})

include(cocos2d/build/BuildHelpers.CMakeLists.txt)

option(USE_CHIPMUNK "Use chipmunk for physics library" ON)
option(USE_BOX2D "Use box2d for physics library" OFF)
option(DEBUG_MODE "Debug or release?" ON)

if(DEBUG_MODE)
  set(CMAKE_BUILD_TYPE DEBUG)
else(DEBUG_MODE)
  set(CMAKE_BUILD_TYPE RELEASE)
endif(DEBUG_MODE)

set(CMAKE_C_FLAGS_DEBUG "-g -Wall -DCOCOS2D_DEBUG=1")
set(CMAKE_CXX_FLAGS_DEBUG ${CMAKE_C_FLAGS_DEBUG})

set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-std=c99")
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} "-std=c++11")

if(USE_CHIPMUNK)
  message("Using chipmunk ...")
  add_definitions(-DLINUX -DCC_ENABLE_CHIPMUNK_INTEGRATION=1)
elseif(USE_BOX2D)
  message("Using box2d ...")
  add_definitions(-DLINUX -DCC_ENABLE_BOX2D_INTEGRATION=1)
else(USE_CHIPMUNK)
  message(FATAL_ERROR "Must choose a physics library.")
endif(USE_CHIPMUNK)

# architecture
if ( CMAKE_SIZEOF_VOID_P EQUAL 8 )
set(ARCH_DIR "64-bit")
else()
set(ARCH_DIR "32-bit")
endif()

AUX_SOURCE_DIRECTORY(Classes/ SRC_1)
AUX_SOURCE_DIRECTORY(./Classes/buzhen SRC_2)
AUX_SOURCE_DIRECTORY(./Classes/employment SRC_3)
AUX_SOURCE_DIRECTORY(./Classes/extentionClass SRC_4)
AUX_SOURCE_DIRECTORY(./Classes/extentionClass/network SRC_NETWORK)
AUX_SOURCE_DIRECTORY(./Classes/haoyou SRC_6)
AUX_SOURCE_DIRECTORY(./Classes/headFile SRC_7)
AUX_SOURCE_DIRECTORY(./Classes/headFile/zhujiemian SRC_8)
AUX_SOURCE_DIRECTORY(./Classes/json/src/lib_json SRC_JSON)
AUX_SOURCE_DIRECTORY(./Classes/liaotian SRC_10)
AUX_SOURCE_DIRECTORY(./Classes/loginregister SRC_11)
AUX_SOURCE_DIRECTORY(./Classes/mycomponent SRC_12)
AUX_SOURCE_DIRECTORY(./Classes/utils SRC_13)
AUX_SOURCE_DIRECTORY(./Classes/xuanzerenwu SRC_14)
set(GAME_SRC
            proj.linux/main.cpp
            Classes/AppDelegate.cpp
            Classes/CData.cpp
            Classes/loginregister/logo.cpp
            Classes/loginregister/LayerLogin.cpp
            ${SRC_NETWORK}
            ${SRC_JSON}
            
       )
set(COCOS2D_ROOT ${CMAKE_SOURCE_DIR}/cocos2d)

include_directories(
  /usr/local/include/GLFW
  Classes
  Classes/loginregister
  Classes/hecheng
  Classes/headFile/zhujiemian
  Classes/extentionClass/network
  ${COCOS2D_ROOT}
  ${COCOS2D_ROOT}/cocos
  ${COCOS2D_ROOT}/cocos/audio/include
  ${COCOS2D_ROOT}/cocos/2d
  ${COCOS2D_ROOT}/cocos/2d/renderer
  ${COCOS2D_ROOT}/cocos/2d/platform
  ${COCOS2D_ROOT}/cocos/2d/platform/desktop
  ${COCOS2D_ROOT}/cocos/2d/platform/linux
  ${COCOS2D_ROOT}/cocos/base
  ${COCOS2D_ROOT}/cocos/physics
  ${COCOS2D_ROOT}/cocos/editor-support
  ${COCOS2D_ROOT}/cocos/math/kazmath/include
  ${COCOS2D_ROOT}/extensions
  ${COCOS2D_ROOT}/external
  ${COCOS2D_ROOT}/external/edtaa3func
  ${COCOS2D_ROOT}/external/jpeg/include/linux
  ${COCOS2D_ROOT}/external/tiff/include/linux
  ${COCOS2D_ROOT}/external/webp/include/linux
  ${COCOS2D_ROOT}/external/tinyxml2
  ${COCOS2D_ROOT}/external/unzip
  ${COCOS2D_ROOT}/external/chipmunk/include/chipmunk
  ${COCOS2D_ROOT}/external/freetype2/include/linux
  ${COCOS2D_ROOT}/external/linux-specific/fmod/include/${ARCH_DIR}
)

link_directories(
  /usr/local/lib
  ${COCOS2D_ROOT}/external/jpeg/prebuilt/linux/${ARCH_DIR}
  ${COCOS2D_ROOT}/external/tiff/prebuilt/linux/${ARCH_DIR}
  ${COCOS2D_ROOT}/external/webp/prebuilt/linux/${ARCH_DIR}
  ${COCOS2D_ROOT}/external/freetype2/prebuilt/linux/${ARCH_DIR}
  ${COCOS2D_ROOT}/external/linux-specific/fmod/prebuilt/${ARCH_DIR}
)

# kazmath
add_subdirectory(${COCOS2D_ROOT}/cocos/math/kazmath)

# chipmunk library
add_subdirectory(${COCOS2D_ROOT}/external/chipmunk/src)

# box2d library
add_subdirectory(${COCOS2D_ROOT}/external/Box2D)

# unzip library
add_subdirectory(${COCOS2D_ROOT}/external/unzip)

# tinyxml2 library
add_subdirectory(${COCOS2D_ROOT}/external/tinyxml2)

# audio
add_subdirectory(${COCOS2D_ROOT}/cocos/audio)

# cocos base library
add_subdirectory(${COCOS2D_ROOT}/cocos/base)

# cocos 2d library
add_subdirectory(${COCOS2D_ROOT}/cocos/2d)

# gui
add_subdirectory(${COCOS2D_ROOT}/cocos/gui)

# network
add_subdirectory(${COCOS2D_ROOT}/cocos/network)

# extensions
add_subdirectory(${COCOS2D_ROOT}/extensions)

## Editor Support

# spine
add_subdirectory(${COCOS2D_ROOT}/cocos/editor-support/spine)

# cocosbuilder
add_subdirectory(${COCOS2D_ROOT}/cocos/editor-support/cocosbuilder)

# cocostudio
add_subdirectory(${COCOS2D_ROOT}/cocos/editor-support/cocostudio)

# add the executable
add_executable(${APP_NAME}
  ${GAME_SRC}
)

if ( CMAKE_SIZEOF_VOID_P EQUAL 8 )
set(FMOD_LIB "fmodex64")
else()
set(FMOD_LIB "fmodex")
endif()

target_link_libraries(${APP_NAME}
  gui
  network
  spine
  cocostudio
  cocosbuilder
  extensions
  audio
  cocos2d
  )

set(APP_BIN_DIR "${CMAKE_BINARY_DIR}/bin")

set_target_properties(${APP_NAME} PROPERTIES
     RUNTIME_OUTPUT_DIRECTORY  "${APP_BIN_DIR}")

pre_build(${APP_NAME}
  COMMAND ${CMAKE_COMMAND} -E remove_directory ${APP_BIN_DIR}/Resources
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/Resources ${APP_BIN_DIR}/Resources
  )

