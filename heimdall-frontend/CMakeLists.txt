cmake_minimum_required(VERSION 3.10.0...4.0.2)

project(heimdall-frontend)

set(LIBPIT_INCLUDE_DIRS
    ../libpit/source)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON) # moc files are generated in build (current) directory

find_package(Qt6Widgets REQUIRED)
find_package(ZLIB REQUIRED)

if(APPLE)
    set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lobjc -framework IOKit -framework CoreFoundation")
endif()

if(MINGW)
    set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++ -static")
endif(MINGW)

include_directories(${LIBPIT_INCLUDE_DIRS})

set(HEIMDALL_FRONTEND_SOURCE_FILES
    source/aboutform.cpp
    source/Alerts.cpp
    source/FirmwareInfo.cpp
    source/main.cpp
    source/mainwindow.cpp
    source/PackageData.cpp
    source/Packaging.cpp)

qt6_wrap_ui(HEIMDALL_FRONTEND_FORMS
    mainwindow.ui
    aboutform.ui)

add_executable(heimdall-frontend WIN32
    MACOSX_BUNDLE
    ${HEIMDALL_FRONTEND_SOURCE_FILES}
    ${HEIMDALL_FRONTEND_FORMS}
    ${HEIMDALL_FRONTEND_RESOURCES})

target_compile_features(heimdall-frontend PRIVATE cxx_std_11)

include(CheckSymbolExists)

#
# Large file support on UN*X, a/k/a LFS.
#
# On Windows, we require _fseeki64() and _ftelli64().  Visual Studio
# has had supported them since Visual Studio 2005/MSVCR80.
#
if(NOT WIN32)
    include(FindLFS)
    if(LFS_FOUND)
        #
        # Add the required #defines.
        #
        add_definitions(${LFS_DEFINITIONS})
    endif()

    #
    # Check for fseeko as well.
    #
    include(FindFseeko)
    if(FSEEKO_FOUND)
        set(HAVE_FSEEKO ON)

        #
        # Add the required #defines.
        #
        add_definitions(${FSEEKO_DEFINITIONS})
    endif()
endif()

set_property(TARGET heimdall-frontend
    APPEND PROPERTY COMPILE_DEFINITIONS "QT_LARGEFILE_SUPPORT")

target_link_libraries(heimdall-frontend pit)
target_link_libraries(heimdall-frontend Qt6::Widgets)
target_link_libraries(heimdall-frontend z)
install (TARGETS heimdall-frontend
        RUNTIME    DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
        LIBRARY    DESTINATION ${CMAKE_INSTALL_LIBDIR}
        BUNDLE DESTINATION /Applications)
