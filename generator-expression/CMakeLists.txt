cmake_minimum_required(VERSION 3.5)

project(generator-expression VERSION 0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets REQUIRED)

cmake_policy(SET CMP0077 NEW)
set(BUILD_EXAMPLES OFF CACHE INTERNAL "")
add_subdirectory("thirdparty")

file(
GLOB PROJECT_SOURCES
  *.cpp
  *.h
  *.ui
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(generator-expression
        WIN32
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET generator-expression APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(generator-expression SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(generator-expression
            WIN32
            ${PROJECT_SOURCES}
        )
    endif()
endif()

target_link_libraries(generator-expression PRIVATE Qt${QT_VERSION_MAJOR}::Widgets)

set(QtADS_target qt${QT_VERSION_MAJOR}advanceddocking)
target_include_directories(generator-expression PRIVATE "$<TARGET_PROPERTY:${QtADS_target},INTERFACE_INCLUDE_DIRECTORIES>")
target_link_libraries(generator-expression PRIVATE ${QtADS_target})

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(generator-expression)
endif()
