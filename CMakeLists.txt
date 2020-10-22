cmake_minimum_required(VERSION 3.30)
project(qjsonmodel  CXX)

###############
# ASI default settings
list(APPEND CMAKE_MODULE_PATH "$ENV{ASV_CMAKE}")
include(asv_cmake_defaults)
asv_cmake_defaults( )
###############

set(libname qjsonmodel)

list(APPEND CMAKE_PREFIX_PATH ${AQTDIR})
set(QT_LIBINFIX "ASV")
find_package(Qt6 CONFIG REQUIRED COMPONENTS Gui)

add_library(${libname} SHARED)
target_link_libraries(${libname} Qt::Gui)
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
