cmake_minimum_required(VERSION 3.24)
include(ExternalProject)

project(llvm-build)

option(STATIC_RUNTIME "Use MSVC static runtime libraries" ON)

if(STATIC_RUNTIME)
    set(LLVM_USE_CRT_RELEASE "MT")
    set(LLVM_USE_CRT_DEBUG "MTd")
    set(LLVM_USE_CRT_RELWITHDEBINFO "MT")
    set(LLVM_USE_CRT_MINSIZEREL "MT")
else()
    set(LLVM_USE_CRT_RELEASE "MD")
    set(LLVM_USE_CRT_DEBUG "MDd")
    set(LLVM_USE_CRT_RELWITHDEBINFO "MD")
    set(LLVM_USE_CRT_MINSIZEREL "MD")
endif()

# For now, target a static LLVM release upstream
# This prevents a re-fetch & re-build at any new commit.
ExternalProject_Add(
  llvm
  GIT_REPOSITORY     https://github.com/llvm/llvm-project.git
  GIT_TAG            main
  GIT_SHALLOW        1
  SOURCE_SUBDIR      llvm
  CMAKE_ARGS         -DLLVM_BUILD_TOOLS=OFF
                     -DLLVM_BUILD_EXAMPLES=OFF
                     -DLLVM_BUILD_BENCHMARKS=OFF
                     -DBUILD_SHARED_LIBS=OFF
                     -DLLVM_TARGETS_TO_BUILD=X86
                     -DLLVM_USE_CRT_RELEASE=${LLVM_USE_CRT_RELEASE}
                     -DLLVM_USE_CRT_DEBUG=${LLVM_USE_CRT_DEBUG}
                     -DLLVM_USE_CRT_RELWITHDEBINFO=${LLVM_USE_CRT_RELWITHDEBINFO}
                     -DLLVM_USE_CRT_MINSIZEREL=${LLVM_USE_CRT_MINSIZEREL}
                     -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                     -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR>/install
  PREFIX             "${CMAKE_BINARY_DIR}/llvm-build"
)