# linuxdeployqt.py
(Unofficial) Qt5 application dependency resolver and deploy tool for linux written in python.

## Description
`linuxdeployqt.py` can resolve shared object (*.so) dependencies of dynamically linked Qt applications.
By using the linux utility `ldd` in a recursive manner `linuxdeployqt.py` can produce a list of (almost) every dependency an executable depends on.

While this approach probably won't work in some scenarios (ex. cross-compiled apps) it's filling in a gap that has been around for a long time; Easy deploying of Linux based Qt software.

## Dependencies

For `linuxdeployqt.py` to do it's magic the following commandline tools must be available.

To execute this script :)
* python

Tools used to inspect system setup
* `ldd`
* `ln`
* `strip`
* `find`
* `grep`

Tools used to produce the final *AppImage*
* patchelf
* AppImageAssistant
* qmlimportscanner
