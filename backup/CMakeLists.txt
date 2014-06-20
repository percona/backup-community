#########################################################
##                                                     ##
##  This is the community edition of toku hot backup.  ##
##                                                     ##
#########################################################

cmake_minimum_required(VERSION 2.8.8)

if (POLICY CMP0043)
  cmake_policy(SET CMP0043 OLD)
endif ()

project(HotBackup)

function(add_space_separated_property type obj propname val)
  get_property(oldval ${type} ${obj} PROPERTY ${propname})
  if (oldval MATCHES NOTFOUND)
    set_property(${type} ${obj} PROPERTY ${propname} "${val}")
  else ()
    set_property(${type} ${obj} PROPERTY ${propname} "${val} ${oldval}")
  endif ()
endfunction(add_space_separated_property)

##set(CMAKE_C_FLAGS   "-Wall -Werror -Wextra -std=c99   ${OPTFLAGS} ${CMAKE_C_FLAGS}")
##set(CMAKE_CXX_FLAGS "-Wall -Werror -Wextra -std=c++11 ${OPTFLAGS} ${CMAKE_CXX_FLAGS}")

include(CheckCCompilerFlag)
include(CheckCXXCompilerFlag)

## adds a compiler flag if the compiler supports it
macro(set_cflags_if_supported_named flag flagname)
  check_c_compiler_flag("${flag}" HAVE_C_${flagname})
  if (HAVE_C_${flagname})
    set(CMAKE_C_FLAGS "${flag} ${CMAKE_C_FLAGS}")
  endif ()
  check_cxx_compiler_flag("${flag}" HAVE_CXX_${flagname})
  if (HAVE_CXX_${flagname})
    set(CMAKE_CXX_FLAGS "${flag} ${CMAKE_CXX_FLAGS}")
  endif ()
endmacro(set_cflags_if_supported_named)

set_cflags_if_supported_named(-Wmissing-declarations MISSING_DECLARATIONS)

set_property(DIRECTORY APPEND PROPERTY COMPILE_DEFINITIONS 
  _FILE_OFFSET_BITS=64 
  _LARGEFILE64_SOURCE)

set(BACKUP_COMMUNITY_SOURCES
        backup_community.cc)

set(HOT_BACKUP_LIBNAME HotBackupCommunity
  CACHE STRING "installed name of the hot backup library")

add_library(${HOT_BACKUP_LIBNAME} SHARED ${BACKUP_COMMUNITY_SOURCES})

# If for some reason we go back to static libraries, we'll need these two:
# set_target_properties(HotBackupCommunity PROPERTIES POSITION_INDEPENDENT_CODE ON)

function(add_space_separated_property type obj propname val)
  get_property(oldval ${type} ${obj} PROPERTY ${propname})
  if (oldval MATCHES NOTFOUND)
    set_property(${type} ${obj} PROPERTY ${propname} "${val}")
  else ()
    set_property(${type} ${obj} PROPERTY ${propname} "${val} ${oldval}")
  endif ()
endfunction(add_space_separated_property)

IF (NOT DEFINED INSTALL_LIBDIR)
    SET(INSTALL_LIBDIR lib)
ENDIF()
install(TARGETS ${HOT_BACKUP_LIBNAME} DESTINATION ${INSTALL_LIBDIR}
  COMPONENT tokubackup_libs_shared)
install(FILES backup.h DESTINATION include
  COMPONENT tokubackup_headers)

## Make this visibile to MySQL.
set(HOT_BACKUP_LIBS ${HOT_BACKUP_LIBNAME} PARENT_SCOPE)

set(OPTFLAGS "-O3 -flto -s")
add_space_separated_property(TARGET ${HOT_BACKUP_LIBNAME} LINK_FLAGS "-flto -s")
#set(OPTFLAGS "")
