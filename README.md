# linuxdeployqt.py
(Unofficial) [Qt5](//qt.io) application binary dependency resolver and deploy tool for linux - written in python.

## Description
`linuxdeployqt.py` can resolve shared object (*.so) dependencies of dynamically linked applications.
While it, in theory, can resolve dependencies for any executable the tool specializes in doing it for Qt5 binaries because some special care is to be taken if you also want the dependencies of dynamically loaded code (Qt plugins, QML imports, etc.).

By using the shell utility `ldd` in a recursive manner `linuxdeployqt.py` can produce a list of (almost) every dependency a Qt executable depends on. 
While this approach probably won't work in some scenarios (ex. cross-compiled apps) it's filling in a gap that has been around for a long time; Easy deploying of Linux based Qt software.

`linuxdeployqt.py` can output the dependency list in JSON format (to a file or stdout) - to use elsewhere - or create an *[AppDir](http://rox.sourceforge.net/desktop/AppDirs.html)* or *AppImage* directly.

*AppDir* and *AppImage* are concepts found in the [AppImageKit project](https://github.com/probonopd/AppImageKit) which can be explored further [here](https://github.com/probonopd/AppImageKit/wiki/AppImageKit-components). Basically the AppImageKit makes it possible to build and deploy universal, batteries included, single-file executables that can run on most linux distros without modifications - a welcomed option in these times of *one-click-should-solve-everything*.

For most cases `linuxdeployqt.py` should be able to find everything needed except for QML loaded dynamically at run time (with e.g.: `Qt.createQmlObject()`). If these run-time imports are unique (i.e. not found in other files) these should be added as values to `--qml-import` parameters (see examples below).


## Dependencies

For `linuxdeployqt.py` to do it's magic the following commandline tools must be available.

**To execute the script**
* python

**Common commandline tools**
These should be installed on most linux distros or at least easy to get via a package tool.
* `ldd`
* `ln`
* `strip`
* `find`
* `grep`

**Resolving dependencies from QML imports**
* qmlimportscanner

**AppImage production**
* patchelf
* AppImageAssistant

