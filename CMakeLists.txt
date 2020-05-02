cmake_minimum_required(VERSION 3.9)

# change the project version according to the version
# in the library source code
project(mongoose VERSION 6.17)

# increase this revision number when building revised
# debian package using the same library version
set(PACKAGE_REVISION 1)

# change this according to the package target architecture
set(PACKAGE_ARCHITECTURE "i386")

set(PACKAGE_VERSION "${PROJECT_VERSION}-${PACKAGE_REVISION}")

set(PACKAGE_NAME "${PROJECT_NAME}${PROJECT_VERSION_MAJOR}")
set(PACKAGE_PATH "${PACKAGE_NAME}_${PACKAGE_VERSION}")

set(PACKAGE_DEV_NAME "${PROJECT_NAME}-dev")
set(PACKAGE_DEV_PATH "${PACKAGE_DEV_NAME}_${PACKAGE_VERSION}")

# get the maintainer name from the git config
execute_process(
  COMMAND git config --global user.name
  COMMAND tr -d '\n'
  OUTPUT_VARIABLE PROJECT_MAINTAINER_NAME
)

# get the maintainer email from the git config
execute_process(
  COMMAND git config --global user.email
  COMMAND tr -d '\n'
  OUTPUT_VARIABLE PROJECT_MAINTAINER_EMAIL
)

set(PACKAGE_MAINTAINER "${PROJECT_MAINTAINER_NAME} <${PROJECT_MAINTAINER_EMAIL}>")

add_library(${PROJECT_NAME} SHARED
  "mongoose/mongoose.c"
  "mongoose/mongoose.h"
)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(${PROJECT_NAME} PROPERTIES SOVERSION ${PROJECT_VERSION_MAJOR})

install(
  FILES "mongoose/mongoose.h"
  DESTINATION "${PACKAGE_DEV_PATH}/usr/include/mongoose"
)

install(
  TARGETS ${PROJECT_NAME}
  DESTINATION "${PACKAGE_PATH}/usr/lib"
)

# configure the package metadata
configure_file("debian/control.in" "package/control" @ONLY)
configure_file("debian/control-dev.in" "package-dev/control" @ONLY)

install(
  FILES "${CMAKE_BINARY_DIR}/package/control"
  DESTINATION "${PACKAGE_PATH}/DEBIAN"
)

install(
  FILES "${CMAKE_BINARY_DIR}/package-dev/control"
  DESTINATION "${PACKAGE_DEV_PATH}/DEBIAN"
)

# copy the license
install(
  FILES "mongoose/LICENSE"
  DESTINATION "${PACKAGE_PATH}/usr/share/doc/${PACKAGE_NAME}"
)
install(
  FILES "mongoose/LICENSE"
  DESTINATION "${PACKAGE_DEV_PATH}/usr/share/doc/${PACKAGE_DEV_NAME}"
)

# copy the examples projects
install(
  DIRECTORY "mongoose/examples"
  DESTINATION "${PACKAGE_DEV_PATH}/usr/share/doc/${PACKAGE_DEV_NAME}"
)