project(devtools-archoream)

## Setup
cmake_minimum_required(VERSION 3.0)
set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH}"
  "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

include(GNUInstallDirs)

function(install_build_script cmd)
  add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/cmds/${cmd}"
    COMMAND "${CMAKE_COMMAND}" -E make_directory
    "${CMAKE_CURRENT_BINARY_DIR}/cmds"
    COMMAND "${CMAKE_COMMAND}" -E create_symlink archbuild
    "${CMAKE_CURRENT_BINARY_DIR}/cmds/${cmd}")
  add_custom_target(${cmd}.target ALL
    DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/cmds/${cmd}")
endfunction()

install_build_script(archoream-i686-build)
install_build_script(archoream-x86_64-build)
install_build_script(archoream-testing-x86_64-build)
install_build_script(archoream-staging-x86_64-build)
install_build_script(archoream-kde-unstable-x86_64-build)
install_build_script(multilib-archoream-build)

foreach(CARCH arm armv6h armv7h aarch64)
  install_build_script("archoream-${CARCH}-build")
  configure_file(archoream-arm.conf.in
    "archoream-${CARCH}.conf" @ONLY)
  install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/archoream-${CARCH}.conf"
    DESTINATION "${CMAKE_INSTALL_DATAROOTDIR}/devtools/pacman.conf.d")
endforeach()

install(DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/cmds/"
  DESTINATION "${CMAKE_INSTALL_BINDIR}")
install(FILES
  "archoream.conf"
  "archoream-testing.conf"
  "archoream-staging.conf"
  "archoream-kde-unstable.conf"
  "multilib-archoream.conf"
  DESTINATION "${CMAKE_INSTALL_DATAROOTDIR}/devtools/pacman.conf.d/")
