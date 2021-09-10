===========
google-re2
===========

.. image:: https://github.com/sarnold/google-re2/workflows/ci/badge.svg
    :target: https://github.com/sarnold/google-re2/actions?query=workflow:ci
    :alt: Action CI Status

A drop-in replacement for the Python re module.

It uses RE2 under the hood, of course, so various PCRE features
(e.g. backreferences, look-around assertions) are not supported.


.. note:: The original source for this module lives on a separate branch in the
          `RE2 library repository`_ in the ``python`` directory.


.. _RE2 library repository: https://github.com/google/re2/tree/abseil/python


Known differences between this API and the re module's API:

* The error class does not provide any error information as attributes.
* The Options class replaces the re module's flags with RE2's options as
  gettable/settable properties. Please see re2.h for their documentation.
* The pattern string and the input string do not have to be the same type.
  Any Text (unicode in Python 2, str in Python 3) will be encoded to UTF-8.
* The pattern string cannot be Text if the options specify Latin-1 encoding.

Requirements for building the C++ extension:

* Building requires RE2, pybind11, and cmake installed in the build
  environment.

  + On Ubuntu/Debian, install cmake, pybind11-dev, and libre2-dev packages
  + On Gentoo, install dev-util/cmake and dev-python/pybind11, then
    dev-libs/re2 `from the embedded overlay`_
  + For a venv you can install the pybind11 package from PyPI

.. _from the embedded overlay: https://github.com/VCTLabs/embedded-overlay


On MacOS, use the ``brew`` package manager::

  $ brew install -s re2 pybind11

On Windows use the ``vcpkg`` package manager::

  $ vcpkg install re2:x64-windows pybind11:x64-windows


With at least Python 3.6 available, install ``tox`` to run the tests
or build wheel packages using different tox environments::

  $ tox  # to run tests on all available python versions
  $ tox -e py  # to run tests on the default system python
  $ tox -e dev  # to install in developer mode and run tests
  $ tox -e deploy  # to build/check sdist and wheel targets


You can pass some cmake environment variables (through tox) to alter the
build type or pass a toolchain file (the latter is required on Windows)
or specify the cmake generator.

::

  $ CMAKE_GENERATOR="Unix Makefiles" CMAKE_TOOLCHAIN_FILE=clang_toolchain.cmake tox -e deploy


*Note about OS/platform integration* - Many Linux distribution package
managers do not build their ``libre2`` packages using cmake and thus do
not install the required cmake config files.  This is the reason for the
note above about using the overlay for Gentoo, and there is also a
corresponding PPA for Ubuntu packages (if you're using Debian you'll
need to build the package from source).

For all Ubuntu series, make sure you have the ``gpg`` and ``add-apt-repository``
commands installed and then add the PPA:

::

  $ sudo apt-get install -y software-properties-common
  $ sudo add-apt-repository -y -s ppa:nerdboy/embedded

Note that on kali you will need to edit the file created under
``/etc/apt/sources.list.d`` for the PPA and change the series name to
``focal``, then run ``sudo apt-get update`` again.

For Gentoo or derivatives based on `Portage`_, first install the portage
overlay.

Create a repos.conf file for the overlay and place the file in the
``/etc/portage/repos.conf`` directory.  Run::

  $ sudo nano /etc/portage/repos.conf/embedded-overlay.conf

and add the following content to the new file::

  [embedded-overlay]

  # Various python ebuilds for embedded
  # Maintainer: nerdboy <nerdboy@gentoo.org>

  location = /var/db/repos/embedded-overlay
  sync-type = git
  sync-uri = https://github.com/VCTLabs/embedded-overlay.git
  priority = 50
  auto-sync = yes

Adjust the path in the ``location`` field as needed, then save and exit nano.

Run the following command to sync the repo::

  $ sudo emaint sync --repo embedded-overlay


.. _Portage: https://wiki.gentoo.org/wiki/Portage
