#########################################################
##                                                     ##
##  This is the community edition of toku hot backup.  ##
##                                                     ##
#########################################################

cmake_minimum_required(VERSION 2.8.8)
project(HotBackup)

#set(OPTFLAGS "-O3 -flto")
set(OPTFLAGS "")

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

add_library(HotBackupCommunity SHARED ${BACKUP_COMMUNITY_SOURCES})
target_link_libraries(HotBackupCommunity LINK_PUBLIC rt)

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

install(TARGETS HotBackupCommunity DESTINATION lib)

## Make this visibile to MySQL.
set(HOT_BACKUP_LIBS HotBackupCommunity PARENT_SCOPE)