==============================================
Fledge operation notification delivery plugin
==============================================

The *fledge-notify-operation* plugin is designed to allow a notification to call an operation on a south service.

`See documentation <docs/index.rst>`_

Build
-----
To build Fledge "operation notification" C++ plugin. First we need to set environment variable NOTIFICATION_SERVICE_INCLUDE_DIRS and set path of include directory from fledge-service-notification source.

.. code-block:: console

  $ export NOTIFICATION_SERVICE_INCLUDE_DIRS=$HOME/fledge-service-notification/C/services/notification/include
  $ mkdir build
  $ cd build
  $ cmake ..
  $ make

- By default the Fledge develop package header files and libraries
  are expected to be located in /usr/include/fledge and /usr/lib/fledge
- If **FLEDGE_ROOT** env var is set and no -D options are set,
  the header files and libraries paths are pulled from the ones under the
  FLEDGE_ROOT directory.
  Please note that you must first run 'make' in the FLEDGE_ROOT directory.

You may also pass one or more of the following options to cmake to override
this default behaviour:

- **FLEDGE_SRC** sets the path of a Fledge source tree
- **FLEDGE_INCLUDE** sets the path to Fledge header files
- **FLEDGE_LIB** sets the path to Fledge libraries
- **FLEDGE_INSTALL** sets the installation path of the plugin

NOTE:
    - The **FLEDGE_INCLUDE** option should point to a location where all the Fledge header files have been installed in a single directory.
    - The **FLEDGE_LIB** option should point to a location where all the Fledge libraries have been installed in a single directory.
    - 'make install' target is defined only when **FLEDGE_INSTALL** is set

**Examples**:

- no options

  $ cmake ..

- no options and FLEDGE_ROOT set

  $ export FLEDGE_ROOT=/some_fledge_setup

  $ cmake ..

- set FLEDGE_SRC

  $ cmake -DFLEDGE_SRC=/home/source/develop/Fledge  ..

- set FLEDGE_INCLUDE

  $ cmake -DFLEDGE_INCLUDE=/dev-package/include ..

- set FLEDGE_LIB

  $ cmake -DFLEDGE_LIB=/home/dev/package/lib ..

- set FLEDGE_INSTALL

  $ cmake -DFLEDGE_INSTALL=/home/source/develop/Fledge ..

  $ cmake -DFLEDGE_INSTALL=/usr/local/fledge ..

