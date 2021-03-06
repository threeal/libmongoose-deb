cmake_minimum_required(VERSION 3.9)

# change the project version according to the version
# in the library source code
project(mongoose VERSION 6.17)

# increase this revision number when building revised
# debian package using the same library version
set(PACKAGE_REVISION 2)

# set the default install path to inside this project
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/../install"
    CACHE PATH "default install path" FORCE
  )
endif()

# set package architecture according to build target
if(CMAKE_SYSTEM_PROCESSOR MATCHES "amd64.*|x86_64.*|AMD64.*")
  set(PACKAGE_ARCHITECTURE "amd64")
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "i686.*|i386.*|x86.*")
  set(PACKAGE_ARCHITECTURE "i386")
else()
  set(PACKAGE_ARCHITECTURE "${CMAKE_SYSTEM_PROCESSOR}")
endif()

set(PACKAGE_VERSION "${PROJECT_VERSION}-${PACKAGE_REVISION}")

set(PACKAGE_NAME "lib${PROJECT_NAME}${PROJECT_VERSION_MAJOR}")
set(PACKAGE_PATH "${PACKAGE_NAME}_${PACKAGE_VERSION}_${PACKAGE_ARCHITECTURE}")

set(PACKAGE_DEV_NAME "lib${PROJECT_NAME}-dev")
set(PACKAGE_DEV_PATH "${PACKAGE_DEV_NAME}_${PACKAGE_VERSION}_${PACKAGE_ARCHITECTURE}")

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
  DESTINATION "${PACKAGE_DEV_PATH}/usr/include"
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