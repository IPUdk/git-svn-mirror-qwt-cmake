#############
Qwt for CMake
#############

|appveyor| |travis|

This repository is a mirror of the official `Qwt SVN repository`_ and
should be updated automatically. We have added some preliminary CMake
scripts to simplify integration with other CMake-based projects. 

If you are looking for a full integration with the 
`hunter package manager`_, you might want to look at `t0p4's mirror`_. Qwt
has been added to the `hunter package manager`_ in November 2018 and is
available since v.0.23.58. 

***************************
Why another Qwt repository?
***************************

It happened a couple of times that we had to switch the Qt version
during the development of a certain piece of software and this would
always require new build of Qwt. To simplify working with many different
Qt versions and to integrate with the rest of the libraries via CMake, 
we added one more Qwt mirror. 

**************
How to use it?
**************

If you have everything configured correctly and if Qt is available within 
CMake, you should simply include this directory in your CMakeLists.txt. 
Note that you can configure static and dynamic builds via the CMake option
:code:`QWT_BUILD_SHARED_LIBS=ON`, but remember to deploy the shared 
library with your project if it not available on the target machine.

.. code-block:: shell

   mkdir build
   cmake .. -G "Visual Studio 15 2017 Win64" -DCMAKE_PREFIX_PATH=C:\Qt\5.9.7\msvc2017_64
   cmake .. -G "Visual Studio 12 2013 Win64" -DCMAKE_PREFIX_PATH=C:\Qt\5.5\msvc2013_64
   cmake --build .

If you only would like to include Qwt in your application, than there is a
macro that should help you to handle the include paths and the deployment
of the shared library:

.. code-block:: cmake

   add_executable(your_executable ${YOUR_SOURCES})
   
   add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/externals/qwt)
   add_qwt_to_target(your_executable)


**********
References
**********

.. target-notes::

.. _`Qwt SVN repository`: https://svn.code.sf.net/p/qwt/code
.. _`hunter package manager`: https://github.com/ruslo/hunter
.. _`t0p4's mirror`: https://github.com/t0p4/Qwt

.. |travis| image:: https://travis-ci.org/IPUdk/git-svn-mirror-qwt-cmake.svg?branch=develop
    :target: https://travis-ci.org/IPUdk/git-svn-mirror-qwt-cmake
    :alt: Travis CI Linux and OSX builds

.. |appveyor| image:: https://ci.appveyor.com/api/projects/status/w7h96e0s3jmsg13a/branch/develop?svg=true
    :target: https://ci.appveyor.com/project/jowr/git-svn-mirror-qwt-cmake/branch/develop
    :alt: AppVeyor Windows builds
