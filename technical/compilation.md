Compiling KADAS Albireo
=======================
- - -

* KADAS Albireo is written in C++ using the Qt 5 framework and extended through Python 2 plugins
* Core functionality implemented as Python plugins:
  * Print
  * Help/About
  * OVL importer (requires proprietary MSS component)

### Getting the source code

 * The KADAS Albireo source code is hosted on GitHub at [https://github.com/sourcepole/kadas-albireo](https://github.com/sourcepole/kadas-albireo)
 * A snapshot of the latest codebase can be downloaded from [https://github.com/sourcepole/kadas-albireo/archive/master.zip](https://github.com/sourcepole/kadas-albireo/archive/master.zip)
 * Alternatively the codebase can be cloned using git:

       git clone https://github.com/sourcepole/kadas-albireo.git


### Build requirements

 * The recommended way to build KADAS Albireo for Windows is by using the `sourcepole/kadas-mingw-buildenv` Docker image:
   * This image contains all the necessary build dependencies with validated versions
   * Docker for Windows requires Windows 10 64bit Pro/Enterprise, build 14393 or later. It can be obtained from [https://docs.docker.com/docker-for-windows/install](https://docs.docker.com/docker-for-windows/install)
   * Docker for Linux can be installed according to [https://docs.docker.com/engine/installation/#server](https://docs.docker.com/engine/installation/#server)
 * KADAS Albireo can also be compiled for any recent Linux distribution

### Building KADAS Albireo for Windows using Docker

 * To compile KADAS Albireo, it is sufficient to run the following command from within the `kadas-albireo` source folder

       docker run -v $PWD:/workspace sourcepole/kadas-mingw-buildenv ms-windows/mingwbuild.sh

 * The application will be compiled in a separate build directory, `kadas-albireo/build_mingw64_qt5`
 * The built application will appear under the build output folder

       kadas-albireo/build_mingw64_qt5/dist/usr/x86_64-w64-mingw32/sys-root/mingw/

   * The contents of this folder contains a self-contained, portable build of KADAS Albireo.

Build system details
====================
- - -

 * `sourcepole/kadas-mingw-buildenv` is a Fedora Linux image with a full MinGW (*Minimalist GNU for Windows*) build environment
   * `kadas-albireo/docker/mingw-buildenv/Dockerfile`
 * `docker run -v $PWD:/workspace sourcepole/kadas-mingw-buildenv ms-windows/mingwbuild.sh`
   * Mounts the current directory (`$PWD`, expected to be the `kadas-albireo` source folder) to `/workspace` inside the Docker container
   * Runs `ms-windows/mingwbuild.sh` inside the Docker container
 * `kadas-albireo/ms-windows/mingwbuild.sh` performs the actual build:
   * Builds the KADAS Albireo binaries, using `cmake` to generate the build makefiles
   * Optimizes the binaries by stripping the debug symbols to separate `*.debug` files
   * Links all library (DLL) and data dependencies to the build output folder

Deployment
==========
- - -

 * The contents of the build output folder contains all the files necessary for launching a bare instance of KADAS Albireo
 * For a full deployment, the following items need to be included:
   * Project templates in `share/qgis/project_templates`, along with any local geodata the project templates may reference
   * Settings templates `settings_full.ini` and `settings_patch.ini` in `share/qgis`
   * Any python plugins, such as the core plugins mentioned above, in `share/qgis/python/plugins`
     * The print plugin is obtainable from [http://pkg.sourcepole.ch/qgis/vbs/kadas_print.zip](http://pkg.sourcepole.ch/qgis/vbs/kadas_print.zip)
     * The help/about plugin is obtainable from [http://pkg.sourcepole.ch/qgis/vbs/kadas_help.zip](http://pkg.sourcepole.ch/qgis/vbs/kadas_help.zip)
     * The ovl plugin is obtainable from [http://pkg.sourcepole.ch/qgis/vbs/kadas_ovl.zip](http://pkg.sourcepole.ch/qgis/vbs/kadas_ovl.zip)
 * These plugins can be added to `share/qgis/python/plugins` inside the build output folder
   * If necessary, CA root certificates in `share/qgis/certificates`
 * The result can be distributed as a portable ZIP, or packaged as an MSI package

Debugging
=========
- - -

 * For low-level debugging, the `gdb` C/C++ debugger is by default bundled with KADAS Albireo
   * `<InstallRoot>\bin\gdb.exe`
 * By default, the binaries contain only minimal debugging information (file and function names only)
 * Full debug symbols (which include source line numbers) are contained in the `*.debug` files, which are generated by the `mingwbuild.sh` build script
   * The official KADAS Albireo release does not install these files by default to save disk space, but they are available in `kadas-portable-debug_<version>.zip`
   * These files need to be placed side-by-side to the respective binaries (i.e. `qgis.exe.debug` in the same folder as `qgis.exe`)
 * Minimal steps for debugging a deployed instance of KADAS Albireo:

       <InstallRoot>\bin\gdb.exe <InstallRoot>\bin\qgis.exe
       run
       # trigger crash
       thread apply all backtrace full
       quit

 * To debug an running instance (i.e. if KADAS Albireo stop responding), the debugger can be attached to a running instance:

       <InstallRoot>\bin\gdb.exe -p <PID of qgis.exe>
       thread apply all backtrace full
       quit

Development
===========
- - -

 * Qt/QGIS development environment (Qt-Creator)
 * C++ Documentation:
   * Qt5: [http://doc.qt.io/qt-5/classes.html](http://doc.qt.io/qt-5/classes.html)
   * QGIS: [http://qgis.org/api/2.8/](http://qgis.org/api/2.8/)
 * PyQGIS:
   * [http://geoapis.sourcepole.com/](http://geoapis.sourcepole.com/)
