cmake_minimum_required(VERSION 3.23)
project(abseil_status_macros VERSION 1.8 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include(FetchContent)  # To allow pulling packages directly from GIT-repositories.

# Load external dependencies to make it available for quberry internals.

# Abseil (advanced C++ language primitives, statuses, etc.)
if (NOT TARGET absl::status)
    message(STATUS "Fetching Abseil from GitHub.")
    FetchContent_Declare(
            absl
            SYSTEM
            GIT_REPOSITORY https://github.com/abseil/abseil-cpp.git
            GIT_TAG 20230802.1
    )
    set(ABSL_INTERNAL_AT_LEAST_CXX17 ON)
    set(ABSL_BUILD_TESTING OFF)
    set(ABSL_USE_SYSTEM_INCLUDES ON)
    set(ABSL_PROPAGATE_CXX_STD ON)
    set(CMAKE_INCLUDE_CURRENT_DIR OFF)  # toggle on/off to handle absl-related warnings.
    FetchContent_MakeAvailable(absl)
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
    set(absl_VERSION 20230802)  # ensure to keep this in check with the used `GIT_TAG`.
endif ()

# Create library
set(SOURCES_ABSL_STATUS_MACROS
        "status.cc"
        "status_builder.cc"
)

set(PUBLIC_HEADERS_STATUS_MACROS
        "absl_status_macros/canonical_errors.h"
        "absl_status_macros/source_location.h"
        "absl_status_macros/status.h"
        "absl_status_macros/status_builder.h"
        "absl_status_macros/status_macros.h"
)

add_library(absl_status_macros STATIC)
target_sources(absl_status_macros PRIVATE ${SOURCES_ABSL_STATUS_MACROS})
target_sources(absl_status_macros PUBLIC FILE_SET HEADERS
        BASE_DIRS ${PROJECT_SOURCE_DIR}
        FILES ${PUBLIC_HEADERS_STATUS_MACROS}
)
target_link_libraries(absl_status_macros PUBLIC absl::memory absl::status absl::statusor absl::strings)
target_compile_definitions(absl_status_macros PRIVATE -DNDEBUG)

# The user would have to provide the full install path.
if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "" CACHE PATH "..." FORCE)
endif ()
install(TARGETS absl_status_macros
        EXPORT absl_status_macros_targets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        FILE_SET HEADERS
)

# abseil status macros unit tests.
# add_subdirectory(tests)
