# doctest
FetchContent_Declare(
  doctest
  GIT_REPOSITORY https://github.com/doctest/doctest.git
  GIT_TAG master
  GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(doctest)

# benchmark
FetchContent_Declare(
  nanobench
  GIT_REPOSITORY https://github.com/martinus/nanobench.git
  GIT_TAG master
  GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(nanobench)

# backward
FetchContent_Declare(
  backward
  GIT_REPOSITORY https://github.com/bombela/backward-cpp
  GIT_TAG master # or a version tag, such as v1.6
  GIT_SHALLOW TRUE
  SYSTEM # optional, the Backward include directory will be treated as system directory
)
FetchContent_MakeAvailable(backward)

file(GLOB_RECURSE all_tests *.cpp)
file(GLOB_RECURSE all_src CONFIGURE_DEPENDS ${PROJECT_SOURCE_DIR}/src/*.cpp)
list(REMOVE_ITEM all_src ${PROJECT_SOURCE_DIR}/src/main.cpp)

foreach(v ${all_tests})
  string(REGEX MATCH "test/.*" relative_path ${v})
  string(REGEX REPLACE "test/" "" target_name ${relative_path})
  string(REGEX REPLACE ".cpp" "" target_name ${target_name})

  add_executable(${target_name} ${v} ${all_src})

  target_compile_features(${target_name} PUBLIC cxx_std_20)
  target_include_directories(${target_name} PUBLIC ${PROJECT_SOURCE_DIR}/include/)
  target_link_libraries(${target_name} PUBLIC doctest_with_main nanobench
    Backward::Backward Backward::Object)
endforeach()