cmake_minimum_required ( VERSION 2.8.8 )
project ( sledge )

set (LUA_LIB_DIR  "")
set (MOAI_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../../..")

#normalizes slashes to forward slashes for windows
if (LIB_PATH)
   get_filename_component(LIB_PATH ${LIB_PATH} ABSOLUTE)
endif (LIB_PATH)
if (HOST_ROOT)
   get_filename_component(HOST_ROOT ${HOST_ROOT} ABSOLUTE) 
else (HOST_ROOT)
	set(HOST_ROOT ${MOAI_ROOT}/src)
endif (HOST_ROOT)

get_filename_component(MY_SOURCE "${CMAKE_CURRENT_SOURCE_DIR}/../../cmake-modules" ABSOLUTE) 
set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${MY_SOURCE}")

if (BUILD_OSX)
    include(platform-osx)
endif (BUILD_OSX)

if (BUILD_WINDOWS)
    include(platform-windows)
endif (BUILD_WINDOWS)

include(platform-common)
include(module-setup)
include(libmoai-setup)

add_subdirectory(../host-modules host-modules)

find_package ( OpenGL REQUIRED )
include_directories ( ${OPENGL_INCLUDE_DIRS} ${HOST_ROOT} )

#fix lua lib path
if (NOT LIB_PATH)
	link_directories(${LUA_LIB_DIR})
endif (NOT LIB_PATH)


set (HUSKY_SRC
    ${HOST_ROOT}/${CUSTOM_HOST}/Husky/shared/HuskyLoader.cpp
  )

set (HUSKY_INCLUDES
    ${HOST_ROOT}/${CUSTOM_HOST}/Husky/shared
)


add_library(husky STATIC ${HUSKY_SRC})
target_include_directories(husky PUBLIC ${HUSKY_INCLUDES})
target_link_libraries(husky moai-core moai-util moai-sim zlcore)


set (MOAI_HUSKY_SRC
  ${HOST_ROOT}/${CUSTOM_HOST}/moai-husky/MOAIHusky.cpp
)

set (MOAI_HUSKY_INCLUDES
  ${HOST_ROOT}/${CUSTOM_HOST}/moai-husky
)

add_library(moai-husky STATIC ${MOAI_HUSKY_SRC})
target_include_directories(moai-husky PUBLIC ${MOAI_HUSKY_INCLUDES})
target_link_libraries(moai-husky moai-core moai-util moai-sim zlcore husky )

set (SLEDGE_SRC
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeCore.cpp
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeGraphicsHandler.cpp
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeHost.cpp
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeHostMain.cpp
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeInputHandler.cpp
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/SledgeInputManager.cpp
)

if (BUILD_OSX)
  set (SLEDGE_SRC ${SLEDGE_SRC}
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/FolderWatcher-mac.mm
  )
endif (BUILD_OSX)

if (BUILD_WINDOWS)
  set (SLEDGE_SRC ${SLEDGE_SRC}
	${HOST_ROOT}/${CUSTOM_HOST}/sledge/FolderWatcher-win.cpp
  )
endif (BUILD_WINDOWS)

set (SLEDGE_INCLUDES
	"${MOAI_ROOT}/src"
	"${MOAI_ROOT}/src/config-default"
  "${MOAI_ROOT}/3rdparty/sdl2-2.0.0/include"
  "${HOST_ROOT}/${CUSTOM_HOST}/"
)

include_directories(${SLEDGE_INCLUDES})
add_executable(sledge ${SLEDGE_SRC})
install(TARGETS sledge DESTINATION bin)
target_link_libraries ( sledge 
  moai-husky
  host-modules
  moai-core
  moai-util
  moai-sim
  SDL2-static
  ${OPENGL_LIBRARIES}
  zlcore
)

if ( BUILD_WINDOWS )
  target_link_libraries ( sledge
    winmm
    Shlwapi
  )
endif (BUILD_WINDOWS)
