# Introduction

Information on how to get ECW working with GDAL is all over the place. This resource aims to provide you with concise instructions to get ECW going through a simple GDAL plugin rather than compiling the entire GDAL library from source --with-ecw.

# Intructions

## Ubuntu 20.04
1. Install GDAL; the below outlined procedure worked well with GDAL 3.1.3 installed from UbuntuGIS-unstable PPA (`sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable`)
2. Download the ERDAS ECW SDK; this guide is based on 5.4 update 1
3. Unzip and make the .bin executatble: `chmod +x ERDAS_ECWJP2_SDK-5.4.0.bin`
4. `./ERDAS_ECWJP2_SDK-5.4.0.bin`
5. `sudo cp -r hexagon/ERDAS-ECW_JPEG_2000_SDK-5.4.0 /usr/local`
6. `sudo ln -s  /usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only/lib/newabi/x64/release/libNCSEcw.so /usr/local/lib/libNCSEcw.so`
7. `sudo ln -s /usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only/lib/newabi/x64/release/libNCSEcw.so.5.4.0 /usr/local/lib/libNCSEcw.so.5.4.0`
8. `sudo ldconfig`
9. Make sure the GDAL plugins directory exists; e.g. /usr/lib/gdalplugins
10. clone this git repository
11. Create a sibling directory to build the plugin: `mkdir libgdal-ecw-build`
12. `cp libgdal-ecw/src/* libgdal-ecw-build`
13. `cd libgdal-ecw-build`
14. `sh ./configure --with-ecw=/usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only --with-autoload=/usr/lib/gdalplugins`
15. `make`
16. `make plugin`
17. `sudo make install`
18. `sudo ldconfig`
19. Now test: `gdalinfo --formats | grep -i ECW`
20. You may need to to tell GDAL where your plugins live: `export GDAL_DRIVER_PATH=/usr/lib/gdalplugins`

With slight adaptation, SDK 5.5 should work fine too.

# Source
Files in src/ were taken from the .DEB: https://launchpad.net/~ubuntugis/+archive/ubuntugis-unstable/+files/libgdal-ecw-src_1.10.0-1~precise4_all.deb (uploaded by Jerome Villeneuve Larouche, 2013-08-21)

Then, the following .CPP and .H files were updated from the official GDAL Github, master branch (2020-10-10; https://github.com/OSGeo/gdal/tree/master/gdal/frmts/ecw):
- ecwasyncreader.cpp
- ecwcreatecopy.cpp
- ecwdataset.cpp
- jp2userbox.cpp
- ecwsdk_headers.h
- gdal_ecw.h

# References
These got me in the right direction:
* http://giswerk.org/doku.php?id=tutorials:softgis:xubuntu:technotes:ecwgdal
* https://adamogrady.id.au/ops/ecw-gdal-ubuntu/
* https://gis.stackexchange.com/questions/94870/unable-to-install-ecw-support-on-lubuntu-14-04

Also, be informed about the new ABI:
* https://trac.osgeo.org/gdal/wiki/ECW#BinaryECWSDKandGCC5.1
* Even Rouault helpful response in [gdal-dev]: https://lists.osgeo.org/pipermail/gdal-dev/2018-May/048478.html

Apparently, since version 4.5 (update 1?) the SDK includes binaries follwing the new C++11 ABI convention