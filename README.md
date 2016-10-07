# linuxdeployqt.py
(Unofficial) [Qt5](//qt.io) application binary dependency resolver and deploy tool for linux - written in python.

There are [other tools](https://github.com/probonopd/linuxdeployqt) in the making that looks very promising and way more agile - so until they mature this little script can hopefully be helpful meanwhile.

This script implement some of the ideas from [linuxdeployqt](https://github.com/probonopd/linuxdeployqt) which builds on logic from [macdeployqt](https://github.com/MaximAlien/macdeployqt)

**IMPORTANT NOTE**
The tool is ATM only tested with Qt applications built with `Qt Creator` downloaded and installed via the Qt Online Installers. I hope it somehow can be used in different setups - but I need help.

## Description
`linuxdeployqt.py` can resolve shared object `(*.so)` dependencies of dynamically linked applications.
While it, *in theory*, can resolve dependencies for any executable (if `ldd` can resolve them) - `linuxdeployqt.py` specializes in doing it for Qt5 binaries because some special care is to be taken if you also want the dependencies of dynamically loaded plugins (Qt plugins, QML imports, etc.).

By using the shell utility `ldd` in a recursive manner `linuxdeployqt.py` can produce a list of (almost) every dependency a Qt executable relies on.
While this approach probably won't work in some scenarios where `ldd` fails (cross-compiled apps?) - it's filling in a gap that has been around for a long time; Easy deploying of Linux based Qt software.

`linuxdeployqt.py` can output the dependency list in JSON format (to a file or stdout) - to use elsewhere - or create an *[AppDir](http://rox.sourceforge.net/desktop/AppDirs.html)* or *AppImage* directly.

*AppDir* and *AppImage* are concepts found in the [AppImageKit project](https://github.com/probonopd/AppImageKit) which can be explored further [here](https://github.com/probonopd/AppImageKit/wiki/AppImageKit-components). Basically the AppImageKit makes it possible to build and deploy universal, batteries included, single-file executables that can run on most linux distros without modifications - a welcomed option in these times of *one-click-should-solve-everything*.

For most cases `linuxdeployqt.py` should be able to find everything needed except for QML loaded dynamically at run time (with e.g.: `Qt.createQmlObject()`). If these run-time imports are unique (i.e. not imported in other files) these should be added as values to `--qml-import` parameters (see examples below).

## Installation


## Dependencies

For `linuxdeployqt.py` to do it's magic the following commandline tools must be available.

**To execute the script**
* python

**Common commandline tools**
* `ldd`
* `ln`
* `strip`
* `find`
* `grep`
These should be installed on most linux distros or at least easy to get via a package tool.

**Resolving dependencies from QML imports**
* qmlimportscanner
The `qmlimportscanner` comes pre-installed with Qt versions installed from the Qt Online Installers. But on some distros it can be a bit tricky to find. As an example it can be found in the [`qtchooser` package](http://packages.ubuntu.com/xenial/all/qtchooser/filelist) in the Ubuntu/Debian repositories.

**AppImage production**
* [patchelf](http://blog.qt.io/blog/2011/10/28/rpath-and-runpath/)
* [AppImageAssistant](https://github.com/probonopd/AppImageKit/releases/tag/6)

To install patchelf:
```
wget https://nixos.org/releases/patchelf/patchelf-0.9/patchelf-0.9.tar.bz2
tar xf patchelf-0.9.tar.bz2
cd patchelf-0.9/ && ./configure  && make && sudo make install
```

To install AppImageAssistant:
```
wget https://github.com/probonopd/AppImageKit/releases/download/6/AppImageAssistant_6-x86_64.AppImage -O AppImageAssistant
chmod +x AppImageAssistant
sudo mv AppImageAssistant /usr/local/bin/
```

## Usage
