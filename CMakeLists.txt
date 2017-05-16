cmake_minimum_required (VERSION 3.1.0)

##
# Hunter Cross-platform package manager
##
include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/mchiasson/hunter/archive/v0.18.47-mchiasson.tar.gz"
    SHA1 "f32df697c8456749551173c56b060c23a70d2183"
    LOCAL
)

project (SDL2_image)

set(SDLIMAGE_SUPPORT_BMP ON CACHE BOOL "Support for BMP file format")
set(SDLIMAGE_SUPPORT_GIF ON CACHE BOOL "Support for GIF file format")
set(SDLIMAGE_SUPPORT_LBM ON CACHE BOOL "Support for LBM file format")
set(SDLIMAGE_SUPPORT_PCX ON CACHE BOOL "Support for PCX file format")
set(SDLIMAGE_SUPPORT_PNM ON CACHE BOOL "Support for PNM file format")
set(SDLIMAGE_SUPPORT_TGA ON CACHE BOOL "Support for TGA file format")
set(SDLIMAGE_SUPPORT_XPM ON CACHE BOOL "Support for XPM file format")
set(SDLIMAGE_SUPPORT_XV ON CACHE BOOL "Support for XV file format")

set(SDLIMAGE_SUPPORT_JPEG ON CACHE BOOL "Support for JPEG file format")
set(SDLIMAGE_SUPPORT_PNG ON CACHE BOOL "Support for PNG file format")
set(SDLIMAGE_SUPPORT_WEBP ON CACHE BOOL "Support for WEBP file format")
set(SDLIMAGE_SUPPORT_TIFF ON CACHE BOOL "Support for TIFF file format")

hunter_add_package(SDL2)
find_package(SDL2 CONFIG REQUIRED)
list(APPEND SDL2_IMAGE_DEPS SDL2::SDL2-static)
include_directories(SDL2)

if(SDLIMAGE_SUPPORT_BMP)
    add_definitions(-DLOAD_BMP)
endif()
if(SDLIMAGE_SUPPORT_GIF)
    add_definitions(-DLOAD_GIF)
endif()
if(SDLIMAGE_SUPPORT_LBM)
    add_definitions(-DLOAD_LBM)
endif()
if(SDLIMAGE_SUPPORT_PCX)
    add_definitions(-DLOAD_PCX)
endif()
if(SDLIMAGE_SUPPORT_PNM)
    add_definitions(-DLOAD_PNM)
endif()
if(SDLIMAGE_SUPPORT_TGA)
    add_definitions(-DLOAD_TGA)
endif()
if(SDLIMAGE_SUPPORT_XPM)
    add_definitions(-DLOAD_XPM)
endif()
if(SDLIMAGE_SUPPORT_XV)
    add_definitions(-DLOAD_XV)
endif()

if(SDLIMAGE_SUPPORT_JPEG)
    add_definitions(-DLOAD_JPG)
    hunter_add_package(Jpeg)
    find_package(JPEG CONFIG REQUIRED)
    list(APPEND SDL2_IMAGE_DEPS JPEG::jpeg)
endif()

if(SDLIMAGE_SUPPORT_PNG)
    add_definitions(-DLOAD_PNG)
    hunter_add_package(PNG)
    find_package(PNG CONFIG REQUIRED)
    list(APPEND SDL2_IMAGE_DEPS PNG::png)
endif()

if(SDLIMAGE_SUPPORT_TIFF)
    add_definitions(-DLOAD_TIF)
    hunter_add_package(TIFF)
    find_package(TIFF CONFIG REQUIRED)
    list(APPEND SDL2_IMAGE_DEPS TIFF::libtiff)
endif()

if(SDLIMAGE_SUPPORT_WEBP)
    add_definitions(-DLOAD_WEBP)
    hunter_add_package(WebP)
    find_package(WebP CONFIG REQUIRED)
    list(APPEND SDL2_IMAGE_DEPS WebP::webp)
endif()

set(SDLIMAGE_SOURCES
    IMG.c
    IMG_bmp.c
    IMG_gif.c
    IMG_jpg.c
    IMG_lbm.c
    IMG_pcx.c
    IMG_png.c
    IMG_pnm.c
    IMG_tga.c
    IMG_tif.c
    IMG_webp.c
    IMG_xcf.c
    IMG_xpm.c
    IMG_xv.c
    IMG_xxx.c
    showimage.c
)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

add_library(${PROJECT_NAME} STATIC
    ${SDLIMAGE_SOURCES}
)

target_link_libraries (${PROJECT_NAME}
    PUBLIC ${SDL2_IMAGE_DEPS}
)

# Install the heades and libraries.
install(
    FILES
        ${CMAKE_CURRENT_SOURCE_DIR}/SDL_image.h
    DESTINATION
        include/SDL2
    COMPONENT
        Development
)

# Create the CMake version file.
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/SDL2_imageConfigVersion.cmake"
  VERSION 2.0.1
  COMPATIBILITY AnyNewerVersion
)

set(targets_export_name "SDL2_imageTargets")

# Create the Config file.
include(CMakePackageConfigHelpers)
set(ConfigPackageLocation lib/cmake/SDL2_image)
configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/SDL2_imageConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/SDL2_imageConfig.cmake
  INSTALL_DESTINATION ${ConfigPackageLocation}
)

install(TARGETS ${PROJECT_NAME}
      EXPORT ${targets_export_name}
      LIBRARY DESTINATION lib COMPONENT Runtime
      ARCHIVE DESTINATION lib COMPONENT Development
      RUNTIME DESTINATION bin COMPONENT Runtime
      PUBLIC_HEADER DESTINATION include COMPONENT Development
      BUNDLE DESTINATION bin COMPONENT Runtime
)

# Install the generated CMake files.
install(EXPORT ${targets_export_name} NAMESPACE "SDL2::" DESTINATION ${ConfigPackageLocation})
install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/SDL2_imageConfigVersion.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/SDL2_imageConfig.cmake"
  DESTINATION ${ConfigPackageLocation}
)

