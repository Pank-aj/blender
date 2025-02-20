# no direct CMake generator expressions to get the install paths of executables.
set(TEST_INSTALL_DIR ${CMAKE_INSTALL_PREFIX_WITH_CONFIG})

# Path to Blender and Python executables for all platforms.
if(MSVC)
  set(TEST_BLENDER_EXE ${TEST_INSTALL_DIR}/blender.exe)
elseif(APPLE)
  set(TEST_BLENDER_EXE ${TEST_INSTALL_DIR}/Blender.app/Contents/MacOS/Blender)
else()
  if(WITH_INSTALL_PORTABLE)
    set(TEST_BLENDER_EXE ${TEST_INSTALL_DIR}/blender)
  else()
    set(TEST_BLENDER_EXE ${TEST_INSTALL_DIR}/bin/blender)
  endif()
endif()

if(NOT TEST_PYTHON_EXE)
  set(TEST_PYTHON_EXE ${PYTHON_EXECUTABLE})
  message(STATUS "Tests: Using Python executable: ${TEST_PYTHON_EXE}")
elseif(NOT EXISTS ${TEST_PYTHON_EXE})
  message(FATAL_ERROR "Tests: TEST_PYTHON_EXE ${TEST_PYTHON_EXE} does not exist")
endif()


# For testing with Valgrind
# set(TEST_BLENDER_EXE valgrind --track-origins=yes --error-limit=no ${TEST_BLENDER_EXE})

# Standard Blender arguments for running tests.
# Specify exit code so that if a Python script error happens, the test fails.
set(TEST_BLENDER_EXE_PARAMS --background -noaudio --factory-startup --debug-memory --debug-exit-on-error --python-exit-code 1)

# Python CTests
if(WITH_BLENDER AND WITH_PYTHON AND NOT WITH_PYTHON_MODULE)
  add_subdirectory(python)
endif()
\
if(WITH_PYTHON_MODULE)
  add_subdirectory(blender_as_python_module)
endif()

# GTest
add_subdirectory(gtests)
