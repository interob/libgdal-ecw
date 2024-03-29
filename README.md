# Introduction

Information on how to get ECW working with GDAL is all over the place. This resource aims to provide you with concise instructions to get ECW going through a simple GDAL plugin rather than compiling the entire GDAL library from source --with-ecw.

This approach is tested on Manjaro using the repo-maintained GDAL package (Official Repository, extra); works like a charm in QGIS (also installed from Official Repository, extra).

# Instructions

## Ubuntu 20.04
1. Install a repo-maintained GDAL. The next steps outlined below worked well with GDAL 3.1.3 installed from UbuntuGIS-unstable PPA (`sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable`) as well as with GDAL 3.7.0 under Manjaro installed from their Official Repository, extra.
2. Download the ERDAS ECW SDK; this guide is based on 5.4 update 1 (http://download.hexagongeospatial.com/downloads/ecw/erdas-ecw-jp2-sdk-v54-update-1-for-linux)
3. Unzip and make the .bin executatble: `chmod +x ERDAS_ECWJP2_SDK-5.4.0.bin`
4. `./ERDAS_ECWJP2_SDK-5.4.0.bin`
5. `sudo cp -r hexagon/ERDAS-ECW_JPEG_2000_SDK-5.4.0 /usr/local`
6. `sudo ln -s  /usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only/lib/newabi/x64/release/libNCSEcw.so /usr/local/lib/libNCSEcw.so`
7. `sudo ln -s /usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only/lib/newabi/x64/release/libNCSEcw.so.5.4.0 /usr/local/lib/libNCSEcw.so.5.4.0`
8. `sudo ldconfig`
9. Make sure the GDAL plugins directory exists; e.g. `/usr/lib/gdalplugins` (used below) or `/usr/local/lib/gdalplugins`
10. clone this git repository
11. Create a sibling directory to build the plugin: `mkdir libgdal-ecw-build`
12. `cp libgdal-ecw/src/* libgdal-ecw-build`
13. `cd libgdal-ecw-build`
14. `autoreconf --force --install -v`
15. `sh ./configure --with-ecw=/usr/local/ERDAS-ECW_JPEG_2000_SDK-5.4.0/Desktop_Read-Only --with-autoload=/usr/lib/gdalplugins`
16. `make`
17. `make plugin`
18. `sudo make install`
19. `sudo ldconfig`
20. Now test: `gdalinfo --formats | grep -i ECW`

With slight adaptation, SDK 5.5 should work fine too. Be aware the you will need to re-do steps 10-20 as you update the GDAL package you have installed in your system.

# Troubleshooting
1. You may need to to tell GDAL where your plugins live: `export GDAL_DRIVER_PATH=/usr/lib/gdalplugins`
2. For use in python you may need to preload libgdal, e.g.: `export LD_PRELOAD=/usr/lib/libgdal.so.27.0.3`

# Source
Files in src/ were taken from the .DEB: https://launchpad.net/~ubuntugis/+archive/ubuntugis-unstable/+files/libgdal-ecw-src_1.10.0-1~precise4_all.deb (uploaded by Jerome Villeneuve Larouche, 2013-08-21)

Then, the following .cpp and .h files were updated from the official GDAL Github, release/3.7 (2023-07-30; https://github.com/OSGeo/gdal/tree/release/3.7/frmts/ecw):
- ecwasyncreader.cpp
- ecwcreatecopy.cpp
- ecwdataset.cpp
- jp2userbox.cpp
- ecwsdk_headers.h
- gdal_ecw.h

# References
These got me in the right direction:
* https://github.com/OSGeo/gdal/blob/master/MIGRATION_GUIDE.TXT
* http://giswerk.org/doku.php?id=tutorials:softgis:xubuntu:technotes:ecwgdal
* https://adamogrady.id.au/ops/ecw-gdal-ubuntu/
* https://gis.stackexchange.com/questions/94870/unable-to-install-ecw-support-on-lubuntu-14-04

Also, be informed about the new ABI:
* https://trac.osgeo.org/gdal/wiki/ECW#BinaryECWSDKandGCC5.1
* Even Rouault helpful response in [gdal-dev]: https://lists.osgeo.org/pipermail/gdal-dev/2018-May/048478.html

Apparently, since version 4.5 (update 1?) the SDK includes binaries follwing the new C++11 ABI convention

# Build GDAL from source with ECW support

Would rather like to build GDAL from source? Check this post (SDK 5.5, minding the C++11 ABI): https://blog.geomaster.pt/compiling-gdal-with-geopdf-support/
