cmake_minimum_required(VERSION 2.8.8)
project(BackupRestore)

include(CTest)

set(BACKUP_TESTS TRUE)

#set(OPTFLAGS "-O3 -flto")
set(OPTFLAGS "")

set(CMAKE_C_FLAGS   "-Wall -Werror -Wextra -std=c99   ${OPTFLAGS} ${CMAKE_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${OPTFLAGS} ${CMAKE_CXX_FLAGS}")

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

option(USE_GCOV "Use gcov for test coverage." OFF)
if (USE_GCOV)
  set(CMAKE_CXX_FLAGS "--coverage ${CMAKE_CXX_FLAGS}")
  set(CMAKE_C_FLAGS   "--coverage ${CMAKE_C_FLAGS}")
  set(CMAKE_EXE_LINKER_FLAGS "--coverage ${CMAKE_EXE_LINKER_FLAGS}")
endif (USE_GCOV)

add_subdirectory(backup)
