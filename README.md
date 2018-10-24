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
 - Java JDK 1.8
   - https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
 - Python 3.2 preferred
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
 - platform-tools-latest-windows
   - https://developer.android.com/studio/releases/platform-tools
 - latest MinGW
   - https://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe/
 - latest Apache Ant 
   - https://ant.apache.org/bindownload.cgi

### Prologue; Initial setup:
- Install notes:
  - If using MinGW install manager, choose: `mingw32-base-bin` and `msys-base-bin`
  - Add JAVA_HOME environment variable pointing to JDK installation directory
  - Add Python installation directory to PATH environment variable
  - After creating environment variables, restart PC.
- Setup project directory for building and easy access:
  - Create the following directories: 
  - `../opencv_project`
  - `../opencv_project/opencv`
  - `../opencv_project/contrib`
  - `../opencv_project/build`
  - `../opencv_project/ndk-r16b`
  - `../opencv_project/platforms`
  - `../opencv_project/platform-tools`
  - `../opencv_project/tools`
  - `../opencv_project/ant`
  - Extract the following zip file contents into their folders:
  - `opencv-master.zip` into `../opencv_project/opencv`
  - `opencv_contrib-master.zip` into `../opencv_project/contrib`
  - `android-ndk-r16b-windows-x86_64.zip` into `../opencv_project/ndk-r16b`
  - `tools-r25.2.5.zip` into `../opencv_project/tools`
  - `platform-tools-latest-windows.zip` into `../opencv_project/platform-tools`
  - `apache-ant-1.10.5-bin.zip` into `../opencv_project/ant`
- Download build-tools and a platform:
  - Open CMD or PowerShell
  - Navigate to `../opencv_project/tools/bin`
  - Execute `.\sdkmanager.bat "build-tools;28.0.3"` 
  - Execute `.\sdkmanager.bat "platforms;android-27"`
  - Note: you can check out all the packages with `.\sdkmanager.bat --list`; Does not really matter what version is picked
- NOTE: When `../opencv_project/tools/` is writen, it is meant as full path

### 1. step; Cmake gui:
- Click `Browse Source...` find and add `../opencv_project/opencv`
- Click `Browse Build...` find and add `.../opencv_project/build`
- Add the following entries by clicking `Add Entry`:
  - Name: `ANDROID_NDK` Type: `Path` Value: `../opencv_project/ndk-r16b`
  - Name: `ANDROID_NATIVE_API_LEVEL` Type: `String` Value: `16`
  - Name: `ANDROID_SDK` Type: `Path` Value: `../opencv_project/`
  - Name: `ANDROID_NDK_HOST_X64` Type: `Bool` Value: `Ticked`
  - Name: `ANT_EXECUTABLE` Type: `File Path` Value: `../opencv_project/ant/bin/ant.bat`
- Click `Configure` (assuming its the first configure with this project)
- In the pop up window:
  - Make sure that `MinGW Makefiles` is the selected generator
  - Make sure that the `Specify toolchain file for cross-compiling` option is selected
  - Click `Next`; Specify toolchain by cliking `...` find and add `../opencv_project/opencv/platforms/android/android.toolchain.cmake`
  - Click `Finish`
- Every thing is RED! 
 - Use `Search box` to check for values, if not found, add them:
 - `ANDROID_SDK_TARGET` Value: the downloaded version (this case `android-27`)
 - `OPENCV_EXTRA_MODULES_PATH` Value: `../opencv_project/contrib/modules`
 - CMAKE_MAKE_PROGRAM file (mingw-dir/bin/mingw32-make.exe)
 - `WITH_CAROTENE` Value: `Not ticked`
 - `BUILD_ZLIB` Value: `Ticked`
- Click `Configure`
- If some entries are still RED, click `Configure` again
- Lastly Click `Generate`

### 2. step; MinGW:
- Navigate to `../MINGW_INSTALL_DIR/msys/1.0`
- Double click the msys.bat
- In the new cmd window:
 - Execute `cd "../opencv_project/build/"`
 - Execute `mingw32-make` (take a while to finish, mine ~20min)
 - Execute `mingw32-make install`
 - Note: In case something was not done correctly `mingw32-make` will fail. If thats the case, it will fail at around 96%, fortunatly the progress is "cached", the second time the command is executed, it will run faster. Surprisingly, the error messages give a decent idea what might be the culprint.

### 3. step; Android Studio:
- Open Android Studio
- Create a new project with these parameters:
  - Select `Include C++ support`
  - Choose minimum `API 23`
  - Pick `Empty Activity`
  - Select C++ standart: `C++11`
- When its done generating the project:
  - Open `File-> New-> Import Module...`
  - As Source directory select `../opencv_project/build/install/sdk/java` (set or remeber a module name)
  - Click `Next`
  - Click `Finish`
- Free to close `import-summary.txt`
- Switch from `Android` to `Project` view
- Expand `opencv_module_name` and `app` directory
- Open `build.gradle` files under previesly expanded directories
- Compare `opencv_module_name` `build.gradle` file to `app` `build.gradle`:
  - Check `opencv_module_name` `build.gradle` `compileSdkVersion` is the same as in `app` `build.gradle`
  - Check `opencv_module_name` `build.gradle` `minSdkVersion` is the same as in `app` `build.gradle`
  - Check `opencv_module_name` `build.gradle` `targetSdkVersion` is the same as in `app` `build.gradle`
  - Click `Sync`
- Check for `jni` directory:
  - Navigate to `app/src/main`
  - If `jni` exists skip creating `jni` directory
- Creating `jni` directory:
  - Right click `main` directory
  - Select `New-> Directory`; Name: `jni`
  - Its color should change to blue, if it does not do the following:
  - Right click `main` directory
  - Select `New-> Folder-> JNI Folder`
  - Select `Change folder Location`
  - Input `src/main/jni` into `New Folder Location`
  - Click `Finish`
- Open `File Explorer` navigate to `../opencv_project/build/install/sdk/native/libs`:
  - Drag and drop the `armeabi-v7a` directory into `jni`
  - Click `OK`
- Open `CMakeList.txt` under `app` directory:
  - Add the following lines after `add_library(...)` and before `find_library(...)`:
  - `include_directories("../opencv_project/build/install/sdk/native/jni/include")`
  - `link_directories("../AndroidStudioProjects/PROJECT_NAME/app/src/main/jniLibs/armeabi-v7a")`
  - Note: Full path is required here
  - Add the following lines after `find_library(...)` and before `target_link_libraries(...)`:
  - `file(GLOB PARTYLIBS "../opencv_project/build/install/sdk/native/3rdparty/libs/armeabi-v7a/*.a")`
  - `file(GLOB CVLIBS  "../opencv_project/build/install/sdk/native/staticlibs/armeabi-v7a/*.a")`
  - Note: Full path is required here
  - Add the following lines inside `target_link_libraries(...)` method:
  - `${CVLIBS}`
  - `${PARTYLIBS}`
  - `${CVLIBS}`
  
