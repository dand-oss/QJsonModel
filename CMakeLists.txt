cmake_minimum_required(VERSION 3.0)
project(qjsonmodel  CXX)

###############
# ASI default settings
list(APPEND CMAKE_MODULE_PATH "$ENV{ASV_CMAKE}")
include(asv_cmake_defaults)
asv_cmake_defaults( )
###############

set(libname qjsonmodel)

set(QT_LIBINFIX "ASV")
find_package(Qt5 CONFIG REQUIRED COMPONENTS Gui)
set(CMAKE_AUTOMOC ON)

add_library(${libname} SHARED)
target_link_libraries(${libname} Qt5::Gui)
set_target_properties(${libname} PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS ON)
if (UNIX)
    # force linker to resolve all deps
    set_target_properties(${libname} PROPERTIES LINK_FLAGS "-Wl,--no-undefined")
endif (UNIX)

target_sources(${libname} PRIVATE
    qjsonmodel.cpp
    qjsonmodel.h
    )

# libraries
install(
    TARGETS ${libname}
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    ARCHIVE DESTINATION lib
)

install(
    FILES qjsonmodel.h
    DESTINATION include/${libname}
    )
