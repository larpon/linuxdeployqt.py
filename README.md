# linuxdeployqt.py
(Unofficial) [Qt5](//qt.io) application binary dependency resolver and deploy tool for linux - written in python.

There are [other tools](https://github.com/probonopd/linuxdeployqt) in the making that looks very promising and way more agile - so until they mature this little script can hopefully be helpful meanwhile.

The script implement some of the ideas from [linuxdeployqt](https://github.com/probonopd/linuxdeployqt) which builds on logic from [macdeployqt](https://github.com/MaximAlien/macdeployqt)

**IMPORTANT NOTES**
ATM the tool is only tested with:
* Python 2.x
* Qt applications built with `Qt Creator` downloaded and installed via the *Qt Online Installers*
* `Kubuntu 17.10`
* `Kubuntu 17.04`
* `Kubuntu 16.10`
* `Kubuntu 16.04`
* `Kubuntu 14.04`

Making it python 3.x compatible should be easy though (only one script).

I hope it somehow can be used in different setups - but I need help testing this.
Submit any issues and let's see if we can get it working :)

## Description
`linuxdeployqt.py` can recursivly resolve shared object `(*.so)` dependencies of dynamically linked applications.
It takes an executable file and find all `*.so` files linked to it - then for each of these `*.so` files it finds their dependencies ... and the dependencies of their dependencies - and so forth (hopefully ending at some point).

While it, *in theory*, can resolve dependencies for any executable (but only if `ldd` can resolve them) - `linuxdeployqt.py` specializes in doing it for Qt5 binaries because some special care is to be taken if you also want the dependencies of dynamically loaded plugins (Qt plugins, QML imports, etc.).

By using the shell utility `ldd` in a recursive manner `linuxdeployqt.py` can produce a list of (almost) every `*.so` dependency a Qt executable relies on.
While this approach probably won't work in some scenarios where `ldd` fails (cross-compiled apps?) - it's filling in a gap that has been around for a long time; Easy deploying of Linux based Qt software - built with `Qt Creator`.

`linuxdeployqt.py` can output the dependency list in JSON format (to a file or stdout) - to use elsewhere - or create an *[AppDir](http://rox.sourceforge.net/desktop/AppDirs.html)* and/or *AppImage* directly.

*AppDir* and *AppImage* are concepts found in the [AppImageKit project](https://github.com/probonopd/AppImageKit) which can be explored further [here](https://github.com/probonopd/AppImageKit/wiki/AppImageKit-components). Basically the AppImageKit makes it possible to build and deploy universal, batteries included, single-file executables that can run on most linux distros without modifications - a welcomed option in these times of *one-click-should-solve-everything*.

For most cases `linuxdeployqt.py` should be able to find everything needed except for QML loaded dynamically at run time (with e.g.: `Qt.createQmlObject()`). If these run-time imports are unique (i.e. not imported in other files) these should be added as values to `--qml-import` parameters (see examples below).

## Installation
```
wget https://raw.githubusercontent.com/Larpon/linuxdeployqt.py/master/linuxdeployqt.py -O linuxdeployqt.py
chmod +x linuxdeployqt.py
```
You can now execute it from current directory
```
./linuxdeployqt.py
or
python ./linuxdeployqt.py
```

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
* [appimagetool](https://github.com/probonopd/AppImageKit/)

To install `patchelf`:
```
wget https://nixos.org/releases/patchelf/patchelf-0.9/patchelf-0.9.tar.bz2
tar xf patchelf-0.9.tar.bz2
cd patchelf-0.9/ && ./configure  && make && sudo make install
```

To install `appimagetool`:
```
wget https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage -O appimagetool
chmod +x appimagetool
sudo mv appimagetool /usr/local/bin/
```

## Usage

If your application can build and run in Qt Creator - theres a good chance that `linuxdeployqt.py` can wrap it directly into an executable *AppImage* that, hopefully, executes flawlessly on Linux distros similair to your own :)

Before running the script there are some obligatory arguments (paths) you need to know before everything `just works™`:
* The Qt *base path* (where Qt is installed)
* The path to your project's QML files (`*.qml`)
* The path to your application executable

**Finding the path to your Qt *base* install**. This is the base directory for ***the Qt version you used to build the application***
If you installed Qt via the online installers and used Qt Creator from the same install to build the application then it should a look like this:
`/home/user/Qt/<QT MAJOR.MINOR version>/gcc_64`
An example:
The application was build against `Qt 5.6.1` in Qt Creator then `/home/user/Qt/5.6/gcc_64` is your Qt *base path*

**The path to your project's QML files**. The top most directory of your project folder (usually where the top `*.pro` file resides)
An example:
`/home/user/Projects/TestApp`

**The path to your application executable**. When building with Qt Creator it creates a build directory above the project directory.
An example:
```
/home/user/Projects/build-TestApp-Desktop_Qt_5_6_1_GCC_64bit2-Release
```
Somewhere within the build directoy your executable will reside.
An example:
```
/home/user/Projects/build-TestApp-Desktop_Qt_5_6_1_GCC_64bit2-Release/TestApp
```

Now using this scheme we can create our `TestApp.AppImage`:
```
./linuxdeployqt.py </path/to/executable> --qt-base-dir </path/to/Qt/install/base> --qml-scan-dir </path/to/project/> --appimage
```
Using the paths from the examples above gives us:
```
./linuxdeployqt.py /home/user/Projects/build-TestApp-Desktop_Qt_5_6_1_GCC_64bit2-Release/TestApp --qt-base-dir /home/user/Qt/5.6/gcc_64 --qml-scan-dir /home/user/Projects/TestApp --appimage
```
**BONUS TIP** All paths can be relative.

If no other arguments are passed to `linuxdeployqt.py` it will per default build an AppDir and AppImage in `/tmp/<executable name>.AppDir` and `/tmp/<executable name>.AppImage`.

Following the example values - you should be able to execute your application by running:
```
cd /tmp/
./TestApp.AppImage
```

## Help
TODO

For a list of available options use `./linuxdeployqt.py -h`

SEO tags: Qt Qt5 QML deploy linux python
