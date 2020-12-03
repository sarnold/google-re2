===========
google-re2
===========


A drop-in replacement for the re module.

It uses RE2 under the hood, of course, so various PCRE features
(e.g. backreferences, look-around assertions) are not supported.


.. note:: The original source for this module lives on a separate branch in the
          `RE2 library repository`_ in the ``python`` subdirectory.


.. _RE2 library repository: https://github.com/google/re2/tree/abseil/python


Known differences between this API and the re module's API:

* The error class does not provide any error information as attributes.
* The Options class replaces the re module's flags with RE2's options as
  gettable/settable properties. Please see re2.h for their documentation.
* The pattern string and the input string do not have to be the same type.
  Any Text (unicode in Python 2, str in Python 3) will be encoded to UTF-8.
* The pattern string cannot be Text if the options specify Latin-1 encoding.

Requirements for building the C++ extension:

* Building requires RE2 to be installed on your system.
  On Debian, for example, install the libre2-dev package.
* Building requires pybind11 to be installed on your system OR venv.
  On Debian, for example, install the pybind11-dev package.
  For a venv, install the pybind11 package from PyPI.
* Building on macOS has not been tested yet and will possibly fail.
* Building on Windows has not been tested yet and will probably fail.
