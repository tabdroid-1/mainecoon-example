cmake_minimum_required(VERSION 3.20)
project(mainecoon-example)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

###################################################################
# Local variables
###################################################################
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
set(PROJECT_SRC_DIR "src")
set(PROJECT_VENDOR_DIR "vendor")
set(PROJECT_ASSETS_DIR "${CMAKE_CURRENT_LIST_DIR}/assets")

macro(append_linker_flags FLAGS)
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${FLAGS}")
endmacro()
###################################################################
# Target sources and create executable
###################################################################
file(GLOB_RECURSE PROJECT_SOURCES
        "${PROJECT_SRC_DIR}/*.h"
        "${PROJECT_SRC_DIR}/*.cpp"
        )

list(APPEND PROJECT_SOURCES
        )


if (ANDROID)
    add_library(${PROJECT_NAME} SHARED ${PROJECT_SOURCES})
else()
    add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
endif()

target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_SRC_DIR})
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_ASSETS_DIR})
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_VENDOR_DIR}/mainecoon/include)
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_VENDOR_DIR}/mainecoon/vendor/spdlog/include)
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_VENDOR_DIR}/mainecoon/vendor/imgui)

add_subdirectory(${PROJECT_VENDOR_DIR}/mainecoon)


target_link_libraries(${PROJECT_NAME}
        mainecoon 
        )

if (EMSCRIPTEN)
	append_linker_flags("-sOFFSCREEN_FRAMEBUFFER=1")
    append_linker_flags("-sMIN_WEBGL_VERSION=2 -sMAX_WEBGL_VERSION=2")
    append_linker_flags("-sUSE_SDL=2")
    append_linker_flags("-FULL_ES3=1")
    append_linker_flags("-lGL")
    append_linker_flags("-lopenal")
    append_linker_flags("-sFORCE_FILESYSTEM=1")
    append_linker_flags("-sFETCH=1")
    append_linker_flags("-sASYNCIFY")
    append_linker_flags("-sASSERTIONS=1")
    append_linker_flags("--preload-file assets")
	append_linker_flags("--use-port=sdl2")
	append_linker_flags("-sPROXY_TO_PTHREAD")
	append_linker_flags("-sALLOW_MEMORY_GROWTH")
	append_linker_flags("-sPTHREAD_POOL_SIZE=1")
	target_link_options(${PROJECT_NAME} PUBLIC -sNO_DISABLE_EXCEPTION_CATCHING)
    target_link_options(${PROJECT_NAME} PUBLIC -pthread)
    target_compile_options(${PROJECT_NAME} PUBLIC -pthread)
 
	append_linker_flags("-O3")
	append_linker_flags("-sMIN_FIREFOX_VERSION=79")
	append_linker_flags("-sMIN_SAFARI_VERSION=150000")
	append_linker_flags("-sMIN_IE_VERSION=0x7FFFFFFF") # Do not support Internet Explorer at all (this is the Emscripten default, shown here for posterity)
	# append_linker_flags("-sMIN_EDGE_VERSION=79")
	append_linker_flags("-sMIN_CHROME_VERSION=80")

    set(CMAKE_EXECUTABLE_SUFFIX ".html")
    set_target_properties(${PROJECT_NAME} PROPERTIES SUFFIX ".html")
    target_compile_definitions(${PROJECT_NAME} PUBLIC ASSETS_PATH="/assets/")
endif()

add_custom_target(copy_assets_mainecoon_example
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${PROJECT_ASSETS_DIR} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/assets
        )

add_dependencies(${PROJECT_NAME} copy_assets_mainecoon_example)











# cmake_minimum_required(VERSION 3.20)
# project(maine-coon-app)
# set(CMAKE_CXX_STANDARD 20)
#
# ###################################################################
# # Local variables
# ###################################################################
# set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
# set(PROJECT_SRC_DIR "src")
# set(PROJECT_ASSETS_DIR "${CMAKE_CURRENT_LIST_DIR}/assets")
#
# # Libraries
#
# if (ANDROID)
#     set(MAINECOON_DIR "${CMAKE_SOURCE_DIR}/../maine-coon")
#     set(MAINECOON_LIB_DIR "${CMAKE_BINARY_DIR}/maine-coon")
# else()
#     set(MAINECOON_DIR "${CMAKE_SOURCE_DIR}/maine-coon")
#     set(MAINECOON_LIB_DIR "${CMAKE_BINARY_DIR}/maine-coon")
# endif()
#
# macro(append_linker_flags FLAGS)
#   set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${FLAGS}")
# endmacro()
# ###################################################################
# # Target sources and create executable
# ###################################################################
# file(GLOB_RECURSE PROJECT_SOURCES
#         "${PROJECT_SRC_DIR}/*.h"
#         "${PROJECT_SRC_DIR}/*.cpp"
#         )
#
# list(APPEND PROJECT_SOURCES
#
#         )
#
# # Create executable
# if (ANDROID)
#     add_library(${PROJECT_NAME} SHARED 
#         ${PROJECT_SOURCES}
#         )
#     include_directories("${MAINECOON_DIR}/vendor/spdlog/include")
#     include_directories("${MAINECOON_DIR}/vendor")
#
#     add_subdirectory(
#         ../maine-coon
#         ./maine-coon
#         )
# else()
#     add_executable(${PROJECT_NAME} "${PROJECT_SOURCES}")
# endif()
# # Link libraries
#
# target_link_libraries(${PROJECT_NAME}
#         maine-coon
#         )
#
# include_directories(SYSTEM "src")
# include_directories("${MAINECOON_DIR}/src")
#
# # Include directories
# target_include_directories(${PROJECT_NAME} PUBLIC
#         "${PROJECT_ASSETS_DIR}"
#         "${PROJECT_SOURCE_DIR}"
#         )
#
# if (MINGW)
#     set(CMAKE_SYSTEM_NAME Windows)
#     set(ENABLE_STATIC_RUNTIME ON)
#     target_compile_options(${PROJECT_NAME} PUBLIC -static)
# endif()
#
# if (EMSCRIPTEN)
#
#     target_link_options(${PROJECT_NAME} PUBLIC -sOFFSCREEN_FRAMEBUFFER=1)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMIN_WEBGL_VERSION=2)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMAX_WEBGL_VERSION=2)
#     target_link_options(${PROJECT_NAME} PUBLIC -sUSE_SDL=2)
#     target_link_options(${PROJECT_NAME} PUBLIC -FULL_ES3=1)
#     target_link_options(${PROJECT_NAME} PUBLIC -lGL)
#     target_link_options(${PROJECT_NAME} PUBLIC -lopenal)
#     target_link_options(${PROJECT_NAME} PUBLIC -sFORCE_FILESYSTEM=1)
#     target_link_options(${PROJECT_NAME} PUBLIC -sFETCH=1)
#     target_link_options(${PROJECT_NAME} PUBLIC -sASYNCIFY)
#     target_link_options(${PROJECT_NAME} PUBLIC -sASSERTIONS=1)
#     target_link_options(${PROJECT_NAME} PUBLIC --use-port=sdl2)
#     target_link_options(${PROJECT_NAME} PUBLIC -sPROXY_TO_PTHREAD)
#     target_link_options(${PROJECT_NAME} PUBLIC -sALLOW_MEMORY_GROWTH)
#     target_link_options(${PROJECT_NAME} PUBLIC -sPTHREAD_POOL_SIZE=1)
#     target_link_options(${PROJECT_NAME} PUBLIC -sNO_DISABLE_EXCEPTION_CATCHING)
#     target_link_options(${PROJECT_NAME} PUBLIC --preload-file assets)
#     target_link_options(${PROJECT_NAME} PUBLIC -pthread)
#     target_compile_options(${PROJECT_NAME} PUBLIC -pthread)
#     target_link_options(${PROJECT_NAME} PUBLIC -O3)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMIN_FIREFOX_VERSION=79)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMIN_SAFARI_VERSION=150000)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMIN_IE_VERSION=0x7FFFFFFF) # Do not support Internet Explorer at all (this is the Emscripten default, shown here for posterity)
#     target_link_options(${PROJECT_NAME} PUBLIC -sMIN_CHROME_VERSION=80)
# 	# target_link_options(${PROJECT_NAME} PUBLIC "-sMIN_EDGE_VERSION=79")
#
# 	# append_linker_flags("-sOFFSCREEN_FRAMEBUFFER=1")
#  #    append_linker_flags("-sMIN_WEBGL_VERSION=2 -sMAX_WEBGL_VERSION=2")
#  #    append_linker_flags("-sUSE_SDL=2")
#  #    append_linker_flags("-FULL_ES3=1")
#  #    append_linker_flags("-lGL")
#  #    append_linker_flags("-lopenal")
#  #    append_linker_flags("-sFORCE_FILESYSTEM=1")
#  #    append_linker_flags("-sFETCH=1")
#  #    append_linker_flags("-sASYNCIFY")
#  #    append_linker_flags("-sASSERTIONS=1")
#  #    append_linker_flags("--preload-file assets")
# 	# append_linker_flags("--use-port=sdl2")
# 	# append_linker_flags("-sPROXY_TO_PTHREAD")
# 	# append_linker_flags("-sALLOW_MEMORY_GROWTH")
# 	# # append_linker_flags("-sPTHREAD_POOL_SIZE_STRICT=0")
# 	# append_linker_flags("-sPTHREAD_POOL_SIZE=1")
# 	# target_link_options(${PROJECT_NAME} PUBLIC -sNO_DISABLE_EXCEPTION_CATCHING)
# 	#
# 	# # append_linker_flags("-sWASM_WORKERS")
#  #    
#  #    target_link_options(${PROJECT_NAME} PUBLIC -pthread)
#  #    target_compile_options(${PROJECT_NAME} PUBLIC -pthread)
#  # 
# 	# append_linker_flags("-O3")
# 	# append_linker_flags("-sMIN_FIREFOX_VERSION=79")
# 	# append_linker_flags("-sMIN_SAFARI_VERSION=150000")
# 	# append_linker_flags("-sMIN_IE_VERSION=0x7FFFFFFF") # Do not support Internet Explorer at all (this is the Emscripten default, shown here for posterity)
# 	# # append_linker_flags("-sMIN_EDGE_VERSION=79")
# 	# append_linker_flags("-sMIN_CHROME_VERSION=80")
# 	#
#     set(CMAKE_EXECUTABLE_SUFFIX ".html")
#     set_target_properties(${PROJECT_NAME} PROPERTIES SUFFIX ".html")
# # 	append_linker_flags("-sOFFSCREEN_FRAMEBUFFER=1")
# # 	append_linker_flags("-sMIN_WEBGL_VERSION=2 -sMAX_WEBGL_VERSION=2")
# #     append_linker_flags("-sUSE_SDL=2")
# #     append_linker_flags("-FULL_ES3=1")
# # 	append_linker_flags("-lGL")
# #     append_linker_flags("-sFORCE_FILESYSTEM=1")
# # 	append_linker_flags("-sFETCH=1")
# # 	append_linker_flags("-sASYNCIFY")
# # 	append_linker_flags("-sASSERTIONS=1")
# #
# # 	append_linker_flags("-Oz")
# # 	append_linker_flags("-sMIN_FIREFOX_VERSION=79")
# # 	append_linker_flags("-sMIN_SAFARI_VERSION=130000")
# # 	append_linker_flags("-sMIN_IE_VERSION=0x7FFFFFFF") # Do not support Internet Explorer at all (this is the Emscripten default, shown here for posterity)
# # 	# append_linker_flags("-sMIN_EDGE_VERSION=79") # Require Chromium-based Edge browser
# # 	append_linker_flags("-sMIN_CHROME_VERSION=80")
# #
# #     set(CMAKE_EXECUTABLE_SUFFIX ".html")
# #     # This line is used to set your executable to build with the emscripten html
# #     # template so that you can directly open it.
# #     set_target_properties(${PROJECT_NAME} PROPERTIES SUFFIX ".html")
# #     target_compile_definitions(${PROJECT_NAME} PUBLIC ASSETS_PATH="/assets/")
# #     # set(CMAKE_EXECUTABLE_SUFFIX ".wasm")
# #     # Set the asset path macro in release mode to a relative path that assumes the
# #     # assets folder is in the same directory as the game executable
# #
# endif (EMSCRIPTEN)
#
# ###################################################################
# # Copy executable dependencies to CMake runtime output directory
# ###################################################################
#
# if (DEFINED EMSCRIPTEN)
#
# endif (DEFINED EMSCRIPTEN)
#
# add_custom_target(copy_assets_sandbox
#         COMMAND ${CMAKE_COMMAND} -E copy_directory
#         ${PROJECT_ASSETS_DIR} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/assets
#         )
#
# add_dependencies(${PROJECT_NAME} copy_assets_sandbox)
# # -----------------------------------------------------------------
