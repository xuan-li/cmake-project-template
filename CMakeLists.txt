cmake_minimum_required(VERSION 3.0)

project(HelloWorld)

set(CMAKE_CXX_STANDARD 14)

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX ${CMAKE_SOURCE_DIR}/install CACHE PATH "cmake install prefix" FORCE)
endif(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

if(MSVC)
  set(CMAKE_DEBUG_POSTFIX "d")
else()
  set(CMAKE_DEBUG_POSTFIX "")
endif()



###################### user-selected option ####################
option(WITH_OPENMP "Enable OpenMP support?" ON)
if(WITH_OPENMP)
 find_package(OpenMP REQUIRED)
 if(OPENMP_FOUND)
   set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
 endif()
 add_definitions(-DWITH_OPENMP)
endif()

####################### header-only dependencies ###################

# set(EIGEN3_INCLUDE_DIR "EIGEN3-NOT-FOUND" CACHE PATH "Eigen3 include dir")
# include_directories(${EIGEN3_INCLUDE_DIR})


####################### precompiled dependencies ####################

# find_package(OpenCV REQUIRED)


##################### import files ################

# if you want to import all:

# file(GLOB SOURCES src/*.cpp)
# file(GLOB HEADERS include/*.h)

####################### add library #########################

add_library(${CMAKE_PROJECT_NAME} include/HelloWorld.h src/HelloWorld.cpp)
target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

# if you need dependencies:
# public mean the libraries are used in public APIs

#target_link_libraries(${CMAKE_PROJECT_NAME} PUBLIC ${OpenCV_LIBRARIES})
#target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC ${OpenCV_INCLUDE_DIRS})

######################## add executable ##########################

add_executable(${CMAKE_PROJECT_NAME}Test src/main.cpp)
target_include_directories(${CMAKE_PROJECT_NAME}Test PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_link_libraries(${CMAKE_PROJECT_NAME}Test PRIVATE HelloWorld)

####################### installing configure ####################
configure_file(PackageConfig.cmake.in ${CMAKE_PROJECT_NAME}Config.cmake @ONLY)
install(TARGETS ${CMAKE_PROJECT_NAME}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

install(TARGETS ${CMAKE_PROJECT_NAME}Test
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

install(FILES ${HEADERS} DESTINATION include)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake DESTINATION .)