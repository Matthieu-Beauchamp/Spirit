# TODO: support installing
# https://www.foonathan.net/2016/03/cmake-install/ 

############################################################
# cmake config
############################################################

cmake_minimum_required(VERSION 3.13)

project(Spirit)
set(SPIRIT_SUPER_PROJECT TRUE)


############################################################
# Build
############################################################

add_library(spirit INTERFACE)

get_directory_property(hasParent PARENT_DIRECTORY)
if(hasParent)
    set(SPIRIT_DEV_BUILD FALSE)
else() # Root project -> dev build
    set(SPIRIT_DEV_BUILD TRUE)
    set(SPIRIT_BASE_BUILD_CATCH TRUE)
    set(SPIRIT_BASE_BUILD_CELERO TRUE)
endif()

# Assumes that modules folder are named: Spirit-Module,
#   that each module defines SPIRIT_MODULE_BUILD_[DOCS, TESTS, EXAMPLES]
#
# moduleName is the second word of Spirit-Module, with the correct case
# devMode is a bool that enables all targets of Spirit-Module when true
macro(spirit_subdirectory moduleName devMode)
    string(TOUPPER ${moduleName} module)
    set(varPrefix SPIRIT_${module})

    if (${devMode} AND SPIRIT_DEV_BUILD)
        set(${varPrefix}_BUILD_TESTS TRUE)
        set(${varPrefix}_BUILD_BENCHMARKS TRUE)
        set(${varPrefix}_BUILD_DOCS TRUE)
        set(${varPrefix}_BUILD_EXAMPLES TRUE)
    endif()

    add_subdirectory(Spirit-${moduleName})
endmacro()

# here the devMode values are subject to change freely.
# No matter their values, projects including Spirit will not see the dev targets
# unless they define the options themselves
spirit_subdirectory(Base TRUE)
spirit_subdirectory(Math TRUE)

target_link_libraries(spirit INTERFACE 
    spirit-base
)