---
title: darknet安卓移植
date: 2019-09-06 19:09:36
tags: [深度学习]
categories: 深度学习
---

## darknet2ncnn安卓移植

项目使用的版本是[老版darknet2ncnn](https://github.com/xiangweizeng/darknet2ncnn/tree/72696c6efc3899d63463be2e9faf4e5b246569ff)，之后作者[适配到了ncnn最新版](https://github.com/xiangweizeng/darknet2ncnn/)。

<!-- more -->

### 编译安卓版ncnn

在linux下编译，查看官方教程即可。注意使用与android studio相同版本的ndk。设置android studio ndk：修改local.properties，ndk.dir=D\:\\softback\\Android\\android-ndk-r15c

### 添加相关源文件

将darknet、ncnn、darknet2ncnn相关源文件和头文件按目录添加到cpp目录下，目录结构如下：

```
├─darknet
│  ├─include
│  └─src
├─darknet2ncnn
│  ├─include
│  └─src
└─ncnn
    ├─include
    └─src
```

darknet源码报错：`compare.c:17:13 error: initializing 'network' (aka 'struct network') with an expression of incompatible type 'network *' (aka 'struct network *'); dereference with *.`修改compare.c，多处对应的指针类型和引用改一下就OK。

在darknet和darknet2ncnn package下new package，添加相关源码，但编译后android studio中该源码目录会显示在cpp目录下，不清楚啥原因，不过不影响编译。

### 编写Cmakelists.txt

这个是重点，很多问题就是没有写好Cmakelists.txt引起的。

```
cmake_minimum_required(VERSION 2.8.10)

set(CMAKE_BUILD_TYPE RELEASE)

set(libs "${CMAKE_SOURCE_DIR}/src/main/jniLibs")
include_directories(${CMAKE_SOURCE_DIR}/src/main/cpp/darknet2ncnn/include
        ${CMAKE_SOURCE_DIR}/src/main/cpp/ncnn/include
        ${CMAKE_SOURCE_DIR}/src/main/cpp/darknet/include
        ${CMAKE_SOURCE_DIR}/src/main/cpp/darknet2ncnn/src
        ${CMAKE_SOURCE_DIR}/src/main/cpp/ncnn/src)

set(CMAKE_STATIC_LINKER_FLAGS "-lm  -pthread -fopenmp -lstdc++")
set(CMAKE_C_FLAGS  "${CMAKE_C_FLAGS} -Ofast -Wno-unused-result  -Wfatal-errors -fPIC -fno-rtti -fno-exceptions")
set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -std=c++11 -Ofast -Wno-unused-result  -Wfatal-errors -fPIC -fno-rtti -fno-exceptions")

add_library (libncnn STATIC IMPORTED)
set_target_properties(libncnn PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/src/main/jniLibs/armeabi-v7a/libncnn.a)

file(GLOB_RECURSE darknet_src ${CMAKE_SOURCE_DIR}/src/main/cpp/darknet/src/*.c)

set(darknet2ncnn_dir ${CMAKE_SOURCE_DIR}/src/main/cpp/darknet2ncnn/src)
set(darknet2ncnn_src ${darknet2ncnn_dir}/layer/darknet_activation.cpp
    ${darknet2ncnn_dir}/layer/darknet_shortcut.cpp
    ${darknet2ncnn_dir}/layer/yolov1_detection.cpp
    ${darknet2ncnn_dir}/layer/yolov3_detection.cpp
    ${darknet2ncnn_dir}/object_detection.cpp
    ${darknet2ncnn_dir}/register_darknet.cpp
    ${darknet2ncnn_dir}/darknet2ncnn.cpp)

set(ncnn_src ${CMAKE_SOURCE_DIR}/src/main/cpp/ncnn/src)

set(lib_src ${darknet_src} ${darknet2ncnn_src} ${CMAKE_SOURCE_DIR}/src/main/cpp/yolov3-tiny-jni.cpp)

add_library( # Sets the name of the library.
        yolov3_tiny_jni

        # Sets the library as a shared library.
        SHARED

        # Provides a relative path to your source file(s).
        ${lib_src})

find_library( # Sets the name of the path variable.
        log-lib

        # Specifies the name of the NDK library that
        # you want CMake to locate.
        log)

target_link_libraries( # Specifies the target library.
        yolov3_tiny_jni
        libncnn
        jnigraphics

        # Links the target library to the log library
        # included in the NDK.
        ${log-lib})

```

### 错误解决

报错：undefined reference to 'typeinfo for ncnn::Layer'，这个在arm-linux交叉编译时遇到过，添加-fno-rtti编译选项即可。

报错：fatal error: use of undeclared identifier 'nullptr'，添加-std=c++11编译选项即可。

make project编译成功，so生成位置：app\build\intermediates\cmake\debug\obj\armeabi-v7a\

build apk报错：Cause: org.jetbrains.plugins.gradle.tooling.util.ModuleComponentIdentifierImpl.getModuleIdentifier()Lorg/gradle/api/artifacts/ModuleIdentifier; 更新android studio即可.

ex.extract返回-100，一般是没找到目标，这里有两种可能，一种是模型正确检测正确，测试图片本来就没目标，另一种情况就是模型有问题，不能正常检测到我们的目标。我反复检查了测试图片和模型都没问题，最后发现还是加载模型错误：

```
private String getPathFromAssets(String assetsFileName){
    File f = new File(getCacheDir()+"/"+assetsFileName);
    if (!f.exists())
        try {
        InputStream is = getAssets().open(assetsFileName);
        int size = is.available();
        byte[] buffer = new byte[size];
        is.read(buffer);
        is.close();
        FileOutputStream fos = new FileOutputStream(f);
        fos.write(buffer);
        fos.close();
    } catch (Exception e) { throw new RuntimeException(e); }
    return f.getPath();
}
```

之前的模型已经加载到缓存中了，后来我更换过一次模型，但是缓存没删除，还是之前的模型，所以一直检测不到目标，将`if (!f.exists())`注释掉，每次初始化都重新写入缓存，最后成功检测到目标。

### 总结

其实也比较简单，主要就写了个Cmakelists.txt。参考以下两项目，感谢作者大佬。

### 参考

[安卓应用层](https://github.com/yuace/yolov2-Tiny-NCNN-Android-demo)

[darknet2ncnn](https://github.com/xiangweizeng/darknet2ncnn/)