cmake_minimum_required(VERSION 3.1.0)

project(CuteCom LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

if(CMAKE_VERSION VERSION_LESS "3.7.0")
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
endif()

find_package(Qt5 COMPONENTS Widgets REQUIRED)

include_directories(${QT_INCLUDES} ${CMAKE_BINARY_DIR})
set(cutecomSrcs main.cpp qcppdialogimpl.cpp)

set(exeType)
set(binInstallDir bin )

if(APPLE)
   # with cmake < 2.6 install(TARGETS ... BUNDLE DESTINATION ... ) doesn't work:
   cmake_minimum_required(VERSION 2.6 FATAL_ERROR )
   set(exeType MACOSX_BUNDLE)
   set(binInstallDir /Applications )
   # hint to linker for indirect referenced libs inside qt3support
   set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -dylib_file QtSql.framework/Versions/4/QtSql:/Library/Frameworks/QtSql.framework/Versions/4/QtSql")
   set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -dylib_file QtNetwork.framework/Versions/4/QtNetwork:/Library/Frameworks/QtNetwork.framework/Versions/4/QtNetwork")
   set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -dylib_file QtXml.framework/Versions/4/QtXml:/Library/Frameworks/QtXml.framework/Versions/4/QtXml")
endif(APPLE)

add_executable(cutecom ${exeType} ${cutecomSrcs} ${uiHeaders})
target_link_libraries(cutecom Qt5::Widgets)

if (APPLE)
   set_target_properties(cutecom PROPERTIES OUTPUT_NAME CuteCom)
endif (APPLE)

install(TARGETS cutecom DESTINATION ${binInstallDir} )

install(FILES cutecom.1 DESTINATION share/man/man1 )

find_program(KDECONFIG_EXECUTABLE NAMES kde-config )

if (KDECONFIG_EXECUTABLE)
   # then ask kde-config for the kde data dirs
   exec_program(${KDECONFIG_EXECUTABLE} ARGS --install apps --expandvars OUTPUT_VARIABLE _apps_DIR )
   install(FILES cutecom.desktop DESTINATION ${_apps_DIR}/Utilities)
endif (KDECONFIG_EXECUTABLE)
