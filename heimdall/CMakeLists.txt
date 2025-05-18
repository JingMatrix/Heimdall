cmake_minimum_required(VERSION 3.10.0...4.0.2)

project(heimdall)

find_package(PkgConfig REQUIRED)
pkg_check_modules(libusb REQUIRED IMPORTED_TARGET libusb-1.0)

set(LIBPIT_INCLUDE_DIRS
    ../libpit/source)

if(APPLE)
    set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lobjc -framework IOKit -framework CoreFoundation")
endif()

if(MINGW)
    set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++ -static")
endif(MINGW)

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    add_definitions(-DOS_LINUX)
endif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")

include_directories(${LIBPIT_INCLUDE_DIRS})

set(HEIMDALL_SOURCE_FILES
    source/Arguments.cpp
    source/BridgeManager.cpp
    source/ClosePcScreenAction.cpp
    source/DetectAction.cpp
    source/DownloadPitAction.cpp
    source/FlashAction.cpp
    source/HelpAction.cpp
    source/InfoAction.cpp
    source/Interface.cpp
    source/main.cpp
    source/PrintPitAction.cpp
    source/Utility.cpp
    source/VersionAction.cpp)

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

add_executable(heimdall ${HEIMDALL_SOURCE_FILES})
target_compile_features(heimdall PRIVATE cxx_std_11)

target_link_libraries(heimdall PRIVATE pit)
target_link_libraries(heimdall PUBLIC PkgConfig::libusb)
install (
  TARGETS heimdall
  RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
