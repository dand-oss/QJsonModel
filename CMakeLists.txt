cmake_minimum_required(VERSION 3.30)
project(qjsonmodel
    LANGUAGES CXX
    VERSION 1.1.0
    DESCRIPTION "Qt JSON Model for Qt applications"
    )

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

# -----------------------
# Public header list
# -----------------------
set(public_headers
    qjsonmodel.h
)

add_library(${libname} SHARED)

# Use file sets for public headers
target_sources(${libname} PUBLIC
    FILE_SET HEADERS
    BASE_DIRS .
    FILES ${public_headers}
)

target_link_libraries(${libname} Qt5::Gui)
set_target_properties(${libname} PROPERTIES
    AUTOMOC ON
    OUTPUT_NAME "Qt5JsonModel")

if(UNIX)
    # force linker to resolve all deps
    set_target_properties(${libname} PROPERTIES LINK_FLAGS "-Wl,--no-undefined")
endif()

if(MSVC)
    # All compile-time flags in one call
    target_compile_options(${libname} PRIVATE
        # Common flags for all configurations
        "-Gw"                        # Global data optimization
        "-Gy"                        # Function-level linking
        "-Zc:inline"                 # Remove unreferenced inline functions
        "-Gd"                        # Use __cdecl (smaller than __stdcall)

        # Debug-specific flags
        $<$<CONFIG:Debug>:-Od>       # Disable optimization for debugging

        # Release-specific flags
        $<$<CONFIG:Release>:-O2>     # Optimize for speed
        $<$<CONFIG:Release>:-Ob2>    # Inline expansion
        $<$<CONFIG:Release>:-GL>     # Whole program optimization
        $<$<CONFIG:Release>:-GF>     # String pooling
        $<$<CONFIG:Release>:-GS->    # Disable security checks
    )

    # Link-time flags
    set_target_properties(${libname} PROPERTIES
        LINK_FLAGS_RELEASE "-LTCG:incremental -OPT:REF -OPT:ICF=10 -OPT:LBR"
        LINK_FLAGS_RELWITHDEBINFO "-LTCG:incremental -OPT:REF -OPT:ICF=5"
        LINK_FLAGS_DEBUG "-OPT:NOREF -OPT:NOICF"  # Keep debug symbols intact
    )
endif()

target_sources(${libname} PRIVATE
    qjsonmodel.cpp
    )

# Install library and headers
install(
    TARGETS ${libname}
    EXPORT ${PROJECT_NAME}Targets
    FILE_SET HEADERS DESTINATION include/${libname}
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    ARCHIVE DESTINATION lib
)

# Install export file
install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION lib/cmake/${PROJECT_NAME}
)

# Generate and install CMake config file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}Config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    @ONLY
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION lib/cmake/${PROJECT_NAME}
)

# CPack configuration for packaging
set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
set(CPACK_PACKAGE_DESCRIPTION "Qt JSON Model for Qt applications")
set(CPACK_PACKAGE_VENDOR "xAI")
set(CPACK_GENERATOR "ZIP;TGZ")
include(CPack)
