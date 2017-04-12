# OBS with RTMPS (secure RTMP)
Open Broadcaster Software (OBS) version 18.0.1 with RTMPS SSL TLS OpenSSL support, compiled to solve problem with facebook server that not accepting normal rtmp.

**Features**
- Added support for RTMPS (SSL/TLS/OpenSSL)

**Requirement**
- Visual C++ Redistributable for Visual Studio 2015
- Visual C++ Redistributable for Visual Studio 2013 

**Download Compiled Binary:** 
- https://github.com/ewwink/OBS-with-RTMPS-SSL-TLS/releases 

----------

**Compile OBS with RTMPS Support**

This build example for `win32`
- install Visual Studio 2013 (Latest update) or Visual Studio 2015
- install Qt: http://www.qt.io/
- install Windows version of cmake: http://www.cmake.org/
- install **Win32 OpenSSL v1.0.2k** (other version may fail): http://slproweb.com/products/Win32OpenSSL.html
- clone official repository, for example to `D:\obs-studio`

`git clone --recursive https://github.com/jp9000/obs-studio.git` 
- Create subdirectories `build32` within the cloned repository for building: 

`D:\obs-studio\build32`.
- Download Pre-built windows dependencies for VS2013 and VS2015 can be found here: 
  - VS2013: https://obsproject.com/downloads/dependencies2013.zip
  - VS2015: https://obsproject.com/downloads/dependencies2015.zip

- extract `win32` folder from dependencies to `D:\obs-studio\deps`
- download VLC Library (*.7z) : http://download.videolan.org/pub/videolan/vlc/last/win32/
- Extract `vlc-2.2.4\sdk\include\vlc` to `D:\obs-studio\deps\win32\include`

- open **`cmage-gui`**

  - In `where is the source code`, enter in the repo directory (example: D:/obs-studio).
  - In `where to build the binaries`, enter the repo directory path with the 'build32' subdirectory (example: D:/obs-studio/build32).

- click **`Add Entry`** with type PATH
  - DepsPath: `C:/obs-studio/deps/win32`
  - QTDIR: `C:/Qt/5.8/msvc2015`

- click **`Configure`**
- when done with no error check `USE_SSL`
 
- edit file `C:\obs-studio\plugins\obs-outputs\CMakeLists.txt` and replace
```
if (USE_SSL)
	find_package(SSL QUIET)
	find_package(ZLIB QUIET)
endif()
```
with the following fix
```
if (USE_SSL)
    find_package(OPENSSL)
    #find_package(SSL)
    find_package(ZLIB)
endif()

message(STATUS "SSL OPENSSL_FOUND: ${OPENSSL_FOUND}")
message(STATUS "SSL ZLIB_FOUND: ${ZLIB_FOUND}")
message(STATUS "SSL OPENSSL_INCLUDE_DIR: ${OPENSSL_INCLUDE_DIR}")

# Map OPENSSL to SSL
set(SSL_FOUND
    ${OPENSSL_FOUND}
    )
set (SSL_INCLUDE_DIRS
    ${OPENSSL_INCLUDE_DIR}
    )
set (SSL_LIBRARIES
    ${OPENSSL_SSL_LIBRARY}
    ${OPENSSL_CRYPTO_LIBRARY}
    )
# END Windows OpenSSL fix
```

- click **`Configure`** again
- sample Configure output 
```
OBS_VERSION: 18.0.1-58-g599ffaa-modified
Found FFmpeg: D:/obs-studio/deps/win32/bin/avcodec.lib (found version "57.70.100") found components:  avcodec avdevice avutil avformat 
Jansson >=2.5 not found, building bundled version
Found FFmpeg: D:/obs-studio/deps/win32/bin/avformat.lib (found version "57.61.100") found components:  avformat avutil swscale swresample avcodec 
Using libavcodec for image loading in libobs
Found FFmpeg: D:/obs-studio/deps/win32/bin/avcodec.lib (found version "57.70.100") found components:  avcodec avutil avformat 
Found FFmpeg: D:/obs-studio/deps/win32/bin/avcodec.lib (found version "57.70.100") found components:  avcodec avutil 
Found LibVLC_INCLUDES: D:/obs-studio/deps/win32/include/vlc  
RSSDK not found, Realsense camera plugin disabled
Using the bundled VST header.
Libfdk not found - obs-libfdk plugin disabled
Found FFmpeg: D:/obs-studio/deps/win32/bin/avcodec.lib (found version "57.70.100") found components:  avcodec avfilter avdevice avutil swscale avformat swresample 
Found FFmpeg: D:/obs-studio/deps/win32/bin/avcodec.lib (found version "57.70.100") found components:  avcodec avutil avformat 
SSL OPENSSL_FOUND: TRUE
SSL ZLIB_FOUND: TRUE
SSL OPENSSL_INCLUDE_DIR: C:/OpenSSL-Win32/include
Configuring done
```
- click **`Generate`** to generate Visual Studio project files in the ` D:/obs-studio/build32` subdirectory.

-  Click **`Open Project`** button and you're now have `obs-studio.sln` to compile with Visual Studio 2013/2015
-  if there error while compile `vlc/libvlc_structures.h not found` edit `D:\obs-studio\deps\win32\include\vlc\libvlc.h` and replace
  
`#include <vlc/libvlc_structures.h>` with `#include <libvlc_structures.h>`
- in visual studio change all **Debug** to **Release** in the **Build > Configuration Manager**

----------


Regards,
ewwink

