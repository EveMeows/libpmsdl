cmake_minimum_required(VERSION 3.15)
project(pmsdl LANGUAGES CXX)

option(LIBPMSDL_COMPILE_EXAMPLE "Compile the map draw example" OFF)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

find_package(SDL3 CONFIG REQUIRED)
find_package(SDL3_image CONFIG REQUIRED)

find_package(nlohmann_json CONFIG REQUIRED)

add_library(${PROJECT_NAME} STATIC
  LibP8M/source/map.cpp
)

target_include_directories(${PROJECT_NAME} PUBLIC
  LibP8M/include
)

target_link_libraries(${PROJECT_NAME} PRIVATE
  SDL3::SDL3
  $<IF:$<TARGET_EXISTS:SDL3_image::SDL3_image-shared>,SDL3_image::SDL3_image-shared,SDL3_image::SDL3_image-static>

  nlohmann_json::nlohmann_json
)

if (LIBPMSDL_COMPILE_EXAMPLE)
  project(MapExample LANGUAGES CXX)
  
  file(COPY Example/Content DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")

  add_executable(MapExample
    Example/main.cpp
    
    Example/renderer.h
    Example/renderer.cpp
  )

  target_link_libraries(${PROJECT_NAME} PRIVATE
    pmsdl
    
    SDL3::SDL3
    $<IF:$<TARGET_EXISTS:SDL3_image::SDL3_image-shared>,SDL3_image::SDL3_image-shared,SDL3_image::SDL3_image-static>
    nlohmann_json::nlohmann_json
  )
endif()
