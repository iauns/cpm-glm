
#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME iauns_cpm_glm)
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
# GLM
#------------------------------------------------------------------------------

# We manually download GLM into our source directory so the user can access
# glm through 'glm/...'. There is no build step since this is a header only
# library.
set(REPO_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/3rdParty/glm")
CPM_EnsureRepoIsCurrent(
  TARGET_DIR ${REPO_SOURCE_DIR}
  GIT_REPOSITORY "https://github.com/g-truc/glm.git"
  GIT_TAG "0.9.4.6"
  )
CPM_ExportAdditionalIncludeDir("${REPO_SOURCE_DIR}")

file( GLOB_RECURSE GLM_HEADERS
  ${REPO_SOURCE_DIR}/*.hpp
)
add_custom_target( ${CPM_TARGET_NAME} SOURCES ${GLM_HEADERS} )

