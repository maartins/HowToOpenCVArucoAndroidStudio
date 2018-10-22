## How to compile the latest OpenCV + Aruco and import into Android Studio.

#### Prerequisites:
 - Windows 10
   - https://www.microsoft.com/en-us/software-download/windows10
 - Cmake 3.11 or greater (32bit)
   - https://cmake.org/download/
 - Python 3.2 and 2.7
   - https://www.python.org/downloads/
 - latest OpenCV & contrib pack
   - https://github.com/opencv/opencv
   - https://github.com/opencv/opencv_contrib
 - Android Studio 3.1.* (this guide probalby works with any version)
   - https://developer.android.com/studio/
 - android-ndk-r16b-windows-x86_64.zip
   - https://dl.google.com/android/repository/android-ndk-r16b-windows-x86_64.zip
 - tools-r25.2.5
   - https://dl.google.com/android/repository/tools_r25.2.5-windows.zip
 - MinGW
   - https://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe/

### Prologue:
- Install Windows 10, Cmake, Python, Android Studio, MinGW
- Add Python to PATH variable in Windows settings, if not done already; Restart PC.
- Setup project directory for building and easy access:
  - Create a folder: `../opencv_project`
  - Create a folder: `../opencv_project/opencv`
  - Create a folder: `../opencv_project/contrib`
  - Create a folder: `../opencv_project/build`
  - Create a folder: `../opencv_project/ndk-r16b`
  - Create a folder: `../opencv_project/tools`
  - Extract `opencv-master.zip` into `../opencv_project/opencv`
  - Extract `opencv_contrib-master.zip` into `../opencv_project/contrib`
  - Extract `android-ndk-r16b-windows-x86_64.zip` into `../opencv_project/ndk-r16b`
  - Extract `tools-r25.2.5.rar` into `../opencv_project/tools`

### 1. step; Cmake gui:
- add
  - ANDROID_NDK dir (ndk-r16b)
  - ANDROID_NDK_HOST_X64 1
  - ANDROID_NATIVE_API_LEVEL 16
  - ANDROID_SDK_TARGET 26
  - ANDROID_SDK_TOOLS dir (tools-r25.2.5)
- click and choose
 - conf -> CMAKE_TOOLCHAIN_FILE file
- check if not as below then change
 - OPENCV_EXTRA_MODULES_PATH dir (contrib-dir/modules)
 - CMAKE_MAKE_PROGRAM file (mingw-dir/bin/mingw32-make.exe)
 - WITH_CAROTENE 0
 - BUILD_ZLIB 1
- configure untill no red marks in cmake gui (ignore warnings in output window)
- generate (ignore warnings in output window)

### 2. step; MinGW:
- go to '../mingw-dir/msys/1.0' and run 'msys' bash file
 - in bash terminal:
 - cd "build"
 - mingw32-make
 - mingw32-make install
