# cmake file

set(BMK_NAME "power")
string(TOLOWER ${BMK_NAME} BMK_NAME_LOWER)
set(BMK_PROJECT_NAME ${BMK_NAME_LOWER})

check_bmk_processing(hasSrcDir)

if(NOT ${hasSrcDir})
  message(STATUS "skipping project: ${BMK_PROJECT_NAME}") 
  return()
endif()

set(FP_TOLERANCE 0.00001)

set(SOURCES
  build.cpp                                       
  compute.cpp
  feasibility.cpp
  power.cpp
  power_original.cpp)

add_prefix(SOURCES "${HARNESS_BMK_SOURCE_SUBDIR}/" "${SOURCES}")

list(APPEND SOURCES ${COMMON_SOURCES})
list(APPEND SOURCES ${COMMON_RAND_SOURCES})

set(TARGET_ABSTRACT ${BMK_PROJECT_NAME}_abstract)

add_executable(${TARGET_ABSTRACT} ${SOURCES})

target_include_directories(${TARGET_ABSTRACT} PUBLIC
  "${CMAKE_CURRENT_BINARY_DIR}")

target_include_directories(${TARGET_ABSTRACT} PUBLIC
  "${CMAKE_CURRENT_SOURCE_DIR}/${HARNESS_BMK_SOURCE_SUBDIR}/")

target_link_libraries(${TARGET_ABSTRACT} m)


# this property is required by our parasitic targets
set_target_properties(${TARGET_ABSTRACT} PROPERTIES LINKER_LANGUAGE C)

# this property is used internally by our harness 
# because benchmark names and their main targets have different names
set_target_properties(${TARGET_ABSTRACT} PROPERTIES BMK_NAME ${BMK_NAME})

#

if(LLVM_IR_UTIL_FOUND)
  foreach(FRAGMENT ${HARNESS_CMAKE_FRAGMENT_FILES})
    include(${FRAGMENT})
  endforeach()
endif()

