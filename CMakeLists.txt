cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME jpeg)
set(CPM_LIB_TARGET_NAME ${CPM_MODULE_NAME})

if ((DEFINED CPM_DIR) AND (DEFINED CPM_UNIQUE_ID) AND (DEFINED CPM_TARGET_NAME))
  set(CPM_LIB_TARGET_NAME ${CPM_TARGET_NAME})
  set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CPM_DIR})
  include(CPM)
else()
  set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
  find_package(Git)
  if(NOT GIT_FOUND)
    message(FATAL_ERROR "CPM requires Git.")
  endif()
  if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
    execute_process(
      COMMAND "${GIT_EXECUTABLE}" clone https://github.com/iauns/cpm ${CPM_DIR}
      RESULT_VARIABLE error_code
      OUTPUT_VARIABLE head_sha)
    if(error_code)
      message(FATAL_ERROR "CPM failed to get the hash for HEAD")
    endif()
  endif()
  include(${CPM_DIR}/CPM.cmake)
endif()

# All externals *must* define this.
CPM_ForceOnlyOneModuleVersion()

CPM_InitModule(${CPM_MODULE_NAME})

#------------------------------------------------------------------------------
# LIBJPEG
#------------------------------------------------------------------------------
set(BUILD_STATIC ON)
set(BUILD_EXCUTABLES OFF)
set(BUILD_TESTS OFF)

set(LIBJPEG_DIR ${CMAKE_CURRENT_SOURCE_DIR}/3rdParty/libjpeg)
set(LIBJPEG_BIN_DIR ${CMAKE_CURRENT_BINARY_DIR}/3rdParty/libjpeg)
CPM_EnsureRepoIsCurrent(
  TARGET_DIR ${LIBJPEG_DIR}
  GIT_REPOSITORY "https://github.com/LuaDist/libjpeg.git"
  USE_CACHING TRUE
  )

add_subdirectory(${LIBJPEG_DIR})

CPM_ExportAdditionalIncludeDir("${LIBJPEG_DIR}")
CPM_ExportAdditionalIncludeDir("${LIBJPEG_BIN_DIR}")
CPM_ExportAdditionalLibraryTarget(jpeg ${LIBJPEG_LIBRARIES})

