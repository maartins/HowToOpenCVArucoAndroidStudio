## How to compile the latest OpenCV + Aruco and import into Android Studio.
By following this guide, hopefully, you will have a working Android Studio project "template" from which you can build up on. Allowing you to create an AR app for Android using Java and/or c++ with JNI.

Credits goes to these links:
 - https://stackoverflow.com/questions/40948953/building-opencv-for-android-and-using-it-with-the-ndk
 - https://stackoverflow.com/questions/26016770/how-to-install-old-version-of-android-build-tools-from-command-line
 - https://jeanvitor.com/cpp-opencv-windonws10-installing/

#### Prerequisites:
 - Windows 10
   - https://www.microsoft.com/en-us/software-download/windows10
 - Cmake 3.11 or greater
   - https://cmake.org/download/
 - Python 3.2 and 2.7
   - https://www.python.org/downloads/
 - latest OpenCV & contrib pack
   - https://github.com/opencv/opencv
   - https://github.com/opencv/opencv_contrib
 - Android Studio 3.1.* (this guide probalby works with any version)
   - https://developer.android.com/studio/
 - android-ndk-r16b-windows-x86_64
   - https://dl.google.com/android/repository/android-ndk-r16b-windows-x86_64.zip
 - tools-r25.2.5
   - https://dl.google.com/android/repository/tools_r25.2.5-windows.zip
 - MinGW
   - https://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe/

### Prologue; Initial setup:
- Install Windows 10, Cmake, Python, Android Studio, MinGW
- In MinGW install manager choose: `mingw32-base-bin` and `msys-base-bin`
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
- Click `Browse Source...` find and add `../opencv_project/opencv`
- Click `Browse Build...` find and add `.../opencv_project/build`
- Add the following entries by clicking `Add Entry`:
  - Name: `ANDROID_NDK` Type: `PATH` Value: `../opencv_project/ndk-r16b`
  - Name: `ANDROID_SDK_TOOLS` Type: `PATH` Value: `../opencv_project/tools`
  - Name: `ANDROID_NDK_HOST_X64` Type: `Bool` Value: `Ticked`
  - Name: `ANDROID_NATIVE_API_LEVEL` Type: `String` Value: `16`
  - Name: `ANDROID_SDK_TARGET` Type: `String` Value: `26`
- Click `Configure` (assuming its the first configure with this project)
- In the pop up window:
  - Make sure that `MinGW Makefiles` is the selected generator
  - Make sure that the `Specify toolchain file for cross-compiling` option is selected
  - Click `Next`; Specify toolchain by cliking `...` find and add `../opencv_project/opencv/platforms/android/android.toolchain.cmake`
  - Click `Finish`
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
