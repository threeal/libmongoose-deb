# Lib Mongoose Deb

This repository contains CMake build project for the Mongoose library.
It was created to accomodate the library packaging using the debian packaging system.

## Usage

### Updating the Source Code

- Initialize and update the `mongoose` submodule.
  ```sh
  $ git submodule init
  $ git submodule update
  ```
- Move to the `mongoose` directory and pull the latest update.
  ```sh
  $ cd mongoose
  $ git pull
  ```
- Change some values in the `CMakeLists.txt` as follow:
  - Update the project version according to the current source code version. _(usually could be seen in the `mongoose/mongoose.h` file)_
  - If the project version does not change and you need to create a revised debian package, increase the `PACKAGE_REVISION` value. Else, reset the value to `1`.

### Building the Library

- Create a build directory and move to it.
  ```sh
  $ mkdir build
  $ cd build
  ```
- Configure Makefile using the following command:
  ```sh
  $ cmake ..
  ```
- Build the library.
  ```sh
  $ make install
  ```
  > Make sure the `install` directory in this project already emptied, else there could be some unused files that may be included in the package.

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