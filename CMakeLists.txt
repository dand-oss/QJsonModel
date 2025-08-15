cmake_minimum_required(VERSION 3.30)
project(qjsonmodel  CXX)

###############
# ASI default settings
list(APPEND CMAKE_MODULE_PATH "$ENV{ASV_CMAKE}")
include(asv_cmake_defaults)
asv_cmake_defaults( )
###############

set(libname qjsonmodel)

find_package(Qt5 CONFIG REQUIRED
    PATHS ${ASV_PLAT_PORTS}/qt5 NO_DEFAULT_PATH
    COMPONENTS
    Gui)
asv_print_package_properties(
    Qt5::Gui
    )

add_library(${libname} SHARED)
target_link_libraries(${libname} Qt5::Gui)
set_target_properties(${libname} PROPERTIES AUTOMOC ON)
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
