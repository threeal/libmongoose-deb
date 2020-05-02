# Mongoose Deb

This repository contains CMake build project for the mongoose library.
It was created to accomodate the library packaging using the debian packaging system.

## Usage

### Updating the Source Code

- Move to the `mongoose` directory and pull the latest update.
  ```sh
  $ cd mongoose
  $ git pull
  ```
- Change some values in the `CMakeLists.txt` as follow:
  - Update the project version according to the current source code version. _(usually could be seen in the `mongoose/mongoose.h` file)_
  - If the project version does not change and you need to create a revised debian package, increase the `PACKAGE_REVISION` value. Else, reset the value to `1`.
  - Change the `PACKAGE_ARCHITECTURE` value according to the build target architecture.

### Building the Library

- Create a build directory and move to it.
  ```sh
  $ mkdir build
  $ cd build
  ```
- Configure Makefile using the following command:
  ```sh
  $ cmake -DCMAKE_INSTALL_PREFIX="../install" ..
  ```
- Build the library.
  ```sh
  $ make install -j4
  ```

### Building the Package

- Back to the project root and move to `install` directory.
  ```sh
  $ cd install
  ```
- Build each directory in the `install` directory as a debian package using the following command:
  ```sh
  $ dpkg-deb --build <directory>
  ```
- If `dpkg-deb` has not been installed, install it using `apt` command.
  ```sh
  $ sudo apt install dpkg-deb
  ```