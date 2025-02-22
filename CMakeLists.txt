if (DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
	set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif ()
if (DEFINED ENV{VCPKG_DEFAULT_TRIPLET} AND NOT DEFINED VCPKG_TARGET_TRIPLET)
	set(VCPKG_TARGET_TRIPLET "$ENV{VCPKG_DEFAULT_TRIPLET}" CACHE STRING "")
endif ()

cmake_minimum_required(VERSION 3.18)
project(Brickmap LANGUAGES CUDA CXX)

if (CMAKE_VERSION VERSION_GREATER_EQUAL 3.18)
  cmake_policy(SET CMP0104 OLD)
endif()

# set(CMAKE_CXX_STANDARD 17)
# set(CMAKE_CUDA_STANDARD 14)
# set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wconversion -Wundef")
# set(CMAKE_CXX_FLAGS_DEBUG "-march=native -g")
# set(CMAKE_CXX_FLAGS_RELEASE "-march=native -g -O3 -ffast-math")

# # Ignore some warnings that add a lot of spam and aren't that critical:
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unused-variable -Wno-unused-parameter")
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-parentheses")

add_subdirectory(external/)
add_definitions(-DGLFW_INCLUDE_NONE)

#find_package(glad CONFIG REQUIRED)
#find_package(glfw3 CONFIG REQUIRED)
#find_package(glm CONFIG REQUIRED)
#find_package(soil2 CONFIG REQUIRED)
#find_package(imgui CONFIG REQUIRED)

# Enable IDE folder support
set_property(GLOBAL PROPERTY USE_FOLDERS ON) 	

# Direct all output to /bin directory
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/bin)


# Auto-detect GPU architecture, sets ${CUDA_ARCHS}
include("cmake/AutoDetectCudaArch.cmake")
#set(CMAKE_CUDA_ARCHITECTURES 61)

add_executable(Brickmap
	"src/assert_cuda.cpp"
	"src/camera.cpp"	
	"src/interop.cpp"
	"src/kernel.cu"
	"src/main.cpp"
	"src/performance_measure.cpp"
	"src/Scene.cpp"
	"src/SimplexNoise.cpp"
	"src/stdafx.cpp"
	"src/sunsky.cu"
	"src/sunsky.cuh"
	"src/variables.cpp"
	"src/voxel.cu"
	"src/voxel.cuh"
	
	"src/assert_cuda.h"
	"src/camera.h"
	"src/cuda_definitions.h"	
	"src/interop.h"
	"src/performance_measure.h"
	"src/Scene.h"
	"src/SimplexNoise.h"
	"src/stdafx.h"
	"src/variables.h"

	#"src/FastNoiseSIMD/FastNoiseSIMD.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD.h"
	#"src/FastNoiseSIMD/FastNoiseSIMD_avx2.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD_avx512.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD_internal.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD_internal.h"
	#"src/FastNoiseSIMD/FastNoiseSIMD_neon.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD_sse2.cpp"
	#"src/FastNoiseSIMD/FastNoiseSIMD_sse41.cpp"
)

target_link_libraries(Brickmap PRIVATE
	glad
	glfw
	glm
	#soil2
	imgui
)

target_include_directories(Brickmap PRIVATE 
	${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}
)
set_target_properties(Brickmap PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} --expt-relaxed-constexpr ${CUDA_ARCHS}")

#target_compile_options(Brickmap PRIVATE                        
#	$<$<COMPILE_LANGUAGE:CXX>:/arch:AVX2>
#)

target_compile_features(Brickmap PUBLIC cxx_std_17)