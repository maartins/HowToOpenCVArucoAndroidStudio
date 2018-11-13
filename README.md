## How to compile the latest OpenCV + Aruco and import into Android Studio.
By following this guide, hopefully, you will have a working Android Studio project "template" from which you can build up on. Allowing you to create an AR app for Android using Java and/or c++ with JNI.

Credits goes to these links:
 - https://stackoverflow.com/questions/40948953/building-opencv-for-android-and-using-it-with-the-ndk
 - https://stackoverflow.com/questions/26016770/how-to-install-old-version-of-android-build-tools-from-command-line
 - https://jeanvitor.com/cpp-opencv-windonws10-installing/

#### Prerequisites:
 | Item | Link |
 |------------|-------------------------------------------------------------|
 | Windows 10 | https://www.microsoft.com/en-us/software-download/windows10 |
 | Cmake 3.11 or greater | https://cmake.org/download/ |
 | Java JDK 1.8 | https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html |
 | Python 3.* preferred | https://www.python.org/downloads/ |
 | latest OpenCV source | https://github.com/opencv/opencv |
 | latest OpenCV contrib source | https://github.com/opencv/opencv_contrib |
 | latest Android Studio | https://developer.android.com/studio/ |
 | android-ndk-r16b-windows-x86_64 | https://dl.google.com/android/repository/android-ndk-r16b-windows-x86_64.zip |
 | tools-r25.2.5 | https://dl.google.com/android/repository/tools_r25.2.5-windows.zip |
 | latest MinGW | https://osdn.net/projects/mingw/releases/p15522 |
 | latest Apache Ant | https://ant.apache.org/bindownload.cgi |

### Prologue; Initial setup:
- Notes:
  - If using MinGW install manager, choose: `mingw32-base-bin` and `msys-base-bin`
  - Add JAVA_HOME environment variable pointing to JDK installation directory
  - Add Python installation directory to PATH environment variable
  - After creating environment variables, restart PC.
- Setup project directory for building and easy access:
  - Create the following directories: 
  - `../opencv_project` (project root directory)
  - `../opencv_project/opencv`
  - `../opencv_project/contrib`
  - `../opencv_project/build`
  - `../opencv_project/ndk-r16b`
  - `../opencv_project/platforms`
  - `../opencv_project/platform-tools`
  - `../opencv_project/tools`
  - `../opencv_project/ant`
- Extract the following zip file contents into their directories:
  - `opencv-master.zip` into `../opencv_project/opencv`
  - `opencv_contrib-master.zip` into `../opencv_project/contrib`
  - `android-ndk-r16b-windows-x86_64.zip` into `../opencv_project/ndk-r16b`
  - `tools-r25.2.5.zip` into `../opencv_project/tools`
  - `apache-ant-1.10.5-bin.zip` into `../opencv_project/ant`
- Download build-tools and platform using `sdkmanager.bat`:
  - Open PowerShell
  - Navigate to `../opencv_project/tools/bin`
  - Execute `.\sdkmanager.bat "build-tools;28.0.3"` 
  - Execute `.\sdkmanager.bat "platforms;android-27"`
  - Execute `.\sdkmanager.bat "platform-tools"`
  - Note: To lookup package names use `.\sdkmanager.bat --list`

### 1. step; Cmake gui; generating the project:
- Open Cmake gui
- Click `Browse Source...` find and add `../opencv_project/opencv`
- Click `Browse Build...` find and add `.../opencv_project/build`
- Add the following entries by clicking `Add Entry`:
  - Name: `ANDROID_NDK` Type: `Path` Value: `../opencv_project/ndk-r16b`
  - Name: `ANDROID_NATIVE_API_LEVEL` Type: `String` Value: `16`
  - Name: `ANDROID_SDK` Type: `Path` Value: `../opencv_project/`
  - Name: `ANDROID_NDK_HOST_X64` Type: `Bool` Value: `Ticked`
  - Name: `ANT_EXECUTABLE` Type: `File Path` Value: `../opencv_project/ant/bin/ant.bat`
- Click `Configure`
- A window will pop up, make sure the following values are selected:
  - `MinGW Makefiles` as selected generator
  - `Specify toolchain file for cross-compiling` option is selected
  - Click `Next`
  - Specify toolchain file location: `../opencv_project/opencv/platforms/android/android.toolchain.cmake`
  - Click `Finish`
- Every thing is RED! 
- Use `Search box` to check the following entries, if not found, add them:
  - `ANDROID_SDK_TARGET` Value: `android-27` (the platform version that was downloaded in Prologue)
  - `OPENCV_EXTRA_MODULES_PATH` Value: `../opencv_project/contrib/modules`
  - `CMAKE_MAKE_PROGRAM` Value: `../MINGW_INSTALL_DIR/bin/mingw32-make.exe`
  - `WITH_CAROTENE` Value: `Not ticked`
  - `BUILD_ZLIB` Value: `Ticked`
- Click `Configure`
- If some entries are still RED, click `Configure` again
- Lastly Click `Generate`

### 2. step; MinGW; compiling the project:
- Navigate to `../MINGW_INSTALL_DIR/msys/1.0`
- Run msys.bat
- In the CMD window:
  - Execute `cd "../opencv_project/build/"`
  - Execute `mingw32-make` (takes a while to finish ~40min)
  - Execute `mingw32-make install`

### 3. step; Android Studio:
- Open Android Studio
- Create a new project with these parameters:
  - Select `Include C++ support`
  - Choose minimum `API 23`
  - Pick `Empty Activity`
  - Select C++ standart: `C++11`
- Note: During the following steps, Android Studio might ask you to install missing packages, do so
- Switch from `Android` to `Project` view
- Make Android Studio use the old ndk:
  - Click `File-> Project Structure...`
  - Select `SDK Location`
  - Set the `Android NDK location` as `../opencv_project/ndk-r16b`
  - Click `OK`
- Import the compiled opencv project as a module:
  - Open `File-> New-> Import Module...`
  - As Source directory select `../opencv_project/build/install/sdk/java`
  - Leave the module name as it is (further in guide `opencv_module_name`)
  - Click `Next`
  - Click `Finish`
- Remove `uses-sdk` tag from `AndroidManifest.xml` located `opencv_module_name/src/main`
- Expand `opencv_module_name`, `app` directories and open both `build.gradle` located under them:
  - Compare `opencv_module_name` `build.gradle` file to `app` `build.gradle`:
  - Check `opencv_module_name` `build.gradle` `compileSdkVersion` is the same as in `app` `build.gradle`
  - Check `opencv_module_name` `build.gradle` `minSdkVersion` is the same as in `app` `build.gradle`
  - Check `opencv_module_name` `build.gradle` `targetSdkVersion` is the same as in `app` `build.gradle`
  - Click `Sync Now`
- Add the opencv project as a dependency to the main app:
  - Open `File-> Project Structure...`:
  - Select `app` from the list
  - Select `Dependecies` tab
  - Click the `+` sign: Select `Module dependecy`
  - Select `opencv_module_name` from the list
  - Click `OK`
  - Click `OK` in Project Structure
- Check for `jni` directory:
  - Navigate to `app/src/main`
  - If `jni` exists skip creating `jni` directory
- Creating `jni` directory:
  - Right click `main` directory
  - Select `New-> Directory`; Name: `jni`
  - `jni` directory color should be blue, if it is not, do the following:
  - Right click `main` directory
  - Select `New-> Folder-> JNI Folder`
  - Select `Change folder Location`
  - Input `src/main/jni` into `New Folder Location`
  - Click `Finish`
- Open `File Explorer` navigate to `../opencv_project/build/install/sdk/native/libs`:
  - Copy the `armeabi-v7a` directory into `app/src/main/jni`
  - Click `OK`
- In `app` `build.gradle` file add the following line after `cmake{...}`:
  - `ndk { abiFilter "armeabi-v7a" }`
  - Click `Sync Now`
- Linking the libraries, open `CMakeList.txt` under `app` directory:
  - Note: Full path is required here; Remeber to change `PROJECT_NAME`
  - Add the following lines after `add_library(...)` and before `find_library(...)`:
  - `include_directories("../opencv_project/build/install/sdk/native/jni/include")`
  - `link_directories("../AndroidStudioProjects/PROJECT_NAME/app/src/main/jni/armeabi-v7a")`
  - Add the following lines after `find_library(...)` and before `target_link_libraries(...)`:
  - `file(GLOB PARTYLIBS "../opencv_project/build/install/sdk/native/3rdparty/libs/armeabi-v7a/*.a")`
  - `file(GLOB CVLIBS  "../opencv_project/build/install/sdk/native/staticlibs/armeabi-v7a/*.a")`
  - Add the following lines inside `target_link_libraries(...)` method between `native-lib` and `${log-lib}`:
  - `${CVLIBS}`
  - `${PARTYLIBS}`
  - `${CVLIBS}`
  - Click `Sync Now`
- Check for errors, navigate to `app/src/main/cpp` and open `native-lib.cpp`:
  - Check if writing `#include <opencv2/aruco.hpp>` works; Autocomplete, no errors?
  - Check if writing `cv::Mat test;` works; Autocomplete, no errors?
- Otherwise, if errors are present, libraries are not linked:
  - Check `CMakeList.txt` for misstypes
  - Check if `libopencv_java{number}.so` is under `jni/armeabi-v7a` directory
  - Check if `../opencv_project/build/install/sdk/native/staticlibs/armeabi-v7a/` contains all the build libs `aruco, etc`
- In the project view, navigate to `app/src/main/java/com.example.me.project_name` and open `MainActivity.java`:
  - Check if writing `import org.opencv.core.Mat;` works;
- If no errors:
  - For OpenCV to work when the app is ran
  - add the following line in `MainActivity.java` after `System.loadLibrary("native-lib");`:
  - `System.loadLibrary("opencv_java4");`
- Note: `opencv_java4` the number depends on the version of opencv source
  
