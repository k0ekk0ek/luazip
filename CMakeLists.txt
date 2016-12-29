# Copyright (C) 2007-2009 LuaDist.
# Submitted by David Manura
# Redistribution and use of this file is allowed according to the
# terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own
# license.

PROJECT(luazip C)
CMAKE_MINIMUM_REQUIRED (VERSION 2.6)

OPTION(LUAZIP_BUILD_SHARED "" ON)
IF(LUAZIP_BUILD_SHARED)
  SET(LUAZIP_LIBRARY_TYPE MODULE)
ENDIF()

# Basic configurations
GET_PROPERTY(LIB64 GLOBAL PROPERTY FIND_LIBRARY_USE_LIB64_PATHS)

if ("${LIB64}" STREQUAL "TRUE")
  SET(LIBSUFFIX 64)
else()
  SET(LIBSUFFIX "")
endif()

SET(INSTALL_LIB_DIR lib${LIBSUFFIX} CACHE PATH "Installation directory for libraries")
MARK_AS_ADVANCED(INSTALL_LIB_DIR)
# / configs

# Find libzzip
  FIND_LIBRARY(LIBZZIP_LIBRARY NAMES zzip)
  FIND_PATH(LIBZZIP_INCLUDE_DIR zzip.h PATH_SUFFIXES include) # Find header
  INCLUDE(FindPackageHandleStandardArgs)
  FIND_PACKAGE_HANDLE_STANDARD_ARGS(libzzip  DEFAULT_MSG  LIBZZIP_LIBRARY LIBZZIP_INCLUDE_DIR)
# / Find libzzip

# Find lua
  FIND_PACKAGE(Lua REQUIRED)
# / Find lua

# Define how to build zip.so:
  INCLUDE_DIRECTORIES(${LIBZZIP_INCLUDE_DIR} ${LUA_INCLUDE_DIR})
  ADD_LIBRARY(zip ${LUAZIP_LIBRARY_TYPE} src/luazip.c src/luazip.def)
  SET_TARGET_PROPERTIES(zip PROPERTIES PREFIX "")
  SET_TARGET_PROPERTIES(zip PROPERTIES OUTPUT_NAME zip)
  TARGET_LINK_LIBRARIES(zip ${LUA_LIBRARIES} ${LIBZZIP_LIBRARY})
# / build zip.so

# Where to install stuff
  INSTALL(TARGETS zip DESTINATION ${INSTALL_LIB_DIR}/lua/${LUA_VERSION_MAJOR}.${LUA_VERSION_MINOR})
# / Where to install.

