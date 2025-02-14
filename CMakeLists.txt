cmake_minimum_required(VERSION 3.17)
project(equalizer)
# Some settings for release
#set(CMAKE_BUILD_TYPE Debug)
#set(CMAKE_C_FLAGS /Z7)
#set(CMAKE_CXX_FLAGS /Z7)

set(CMAKE_CXX_STANDARD 17)

# add SDL lib
add_subdirectory(SDL/)
include_directories("./SDL/include")

# add SDL mixer
add_subdirectory(SDL_mixer/)
include_directories("./SDL_mixer/include")
include_directories(./SDL2_ttf-2.0.15/include)


#include_directories("${PROJECT_SOURCE_DIR}/SDL_ttf/")
#add_subdirectory(${PROJECT_SOURCE_DIR}/SDL_ttf/)



add_library(graphics
        components/graphics/window/drawing_stuff.h components/graphics/window/drawing_stuff.cc
        components/graphics/window/equalizer_window.cc components/graphics/window/equalizer_window.h
        components/graphics/window/fps_counter.h components/graphics/window/fps_counter.cc
        components/graphics/canvas/rgb_color.h components/graphics/canvas/rgb_color.cc
        components/graphics/canvas/canvas.h components/graphics/canvas/canvas.cc
        components/graphics/canvas/coord.h components/graphics/canvas/coord.cc
        components/graphics/Buttons/canvas_button.h components/graphics/Buttons/canvas_button.cc
        components/graphics/ppm_loader/file_exception.h
        )

add_library(audio
        components/audio/audio_progress.h components/audio/audio_progress.cc
        components/audio/audio_playback.h components/audio/audio_playback.cc
        components/audio/fourier_transform.cc)

add_library(file_dialogs
        tinyfiledialogs/tinyfiledialogs.c tinyfiledialogs/tinyfiledialogs.h
        )

add_executable(equalizer main.cc ${FILE_DIALOG} tiny_message.h)
target_link_libraries(graphics
        SDL2
        SDL2::SDL2main)

target_link_libraries(
        equalizer
        SDL2
        SDL2::SDL2main
        file_dialogs
        graphics
        audio
)

find_library(SDL_TTF SDL2TTF SDL2_TTF SDL_Ttf SDL_ttf SDL2TTF_LIBRARY  REQUIRED PATHS ${PROJECT_SOURCE_DIR}/SDL2_ttf-2.0.15/lib/x64)
target_link_libraries(equalizer ${SDL_TTF})

if (WIN32)
    set(SDL_SHARED SDL2d.dll)
endif (WIN32)
if (UNIX)
    set(SDL_SHARED libSDL2d.so)
endif (UNIX)

message(STATUS "Copying shared lib to out folder")

# Copy shared libs to out folder
#add_custom_command(
#        TARGET equalizer POST_BUILD                     # Adds a post-build event to equalizer
#        COMMAND ${CMAKE_COMMAND} -E copy_if_different   # which executes "cmake - E copy_if_different..."
#        "$<TARGET_FILE_DIR:equalizer>/SDL/${SDL_SHARED}"    # <--this is in-file
#        $<TARGET_FILE_DIR:equalizer>                    # <--this is out-file path
#)
#add_custom_command(
#        TARGET equalizer POST_BUILD                     # Adds a post-build event to equalizer
#        COMMAND ${CMAKE_COMMAND} -E copy_if_different   # which executes "cmake - E copy_if_different..."
#        "$<TARGET_FILE_DIR:equalizer>/SDL/SDL2.dll"    # <--this is in-file
#        $<TARGET_FILE_DIR:equalizer>                    # <--this is out-file path
#)
add_custom_command(
        TARGET equalizer POST_BUILD                     # Adds a post-build event to equalizer
        COMMAND ${CMAKE_COMMAND} -E copy_if_different   # which executes "cmake - E copy_if_different..."
        "${PROJECT_SOURCE_DIR}/SDL2_ttf-2.0.15/lib/x64/SDL2_ttf.dll"    # <--this is in-file
        $<TARGET_FILE_DIR:equalizer>                    # <--this is out-file path
)
add_custom_command(
        TARGET equalizer POST_BUILD                     # Adds a post-build event to equalizer
        COMMAND ${CMAKE_COMMAND} -E copy_if_different   # which executes "cmake - E copy_if_different..."
        "${PROJECT_SOURCE_DIR}/SDL2_ttf-2.0.15/lib/x64/libfreetype-6.dll"    # <--this is in-file
        $<TARGET_FILE_DIR:equalizer>                    # <--this is out-file path
)
add_custom_command(
        TARGET equalizer POST_BUILD                     # Adds a post-build event to equalizer
        COMMAND ${CMAKE_COMMAND} -E copy_if_different   # which executes "cmake - E copy_if_different..."
        "${PROJECT_SOURCE_DIR}/SDL2_ttf-2.0.15/lib/x64/zlib1.dll"    # <--this is in-file
        $<TARGET_FILE_DIR:equalizer>                    # <--this is out-file path
)

# Copy example wav file to out folder
add_custom_command(
        TARGET equalizer POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PROJECT_SOURCE_DIR}/files/example.wav"
        $<TARGET_FILE_DIR:equalizer>
)
