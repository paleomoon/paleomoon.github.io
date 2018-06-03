---
title: 安卓使用zbar扫描二维码
date: 2018-06-03 21:53:54
tags: [安卓]
categories: 安卓
---

最近需要利用安卓开发板连接USB摄像头进行二维码扫描，一般大家都使用的是zxing和zbar两个开源库，但一般zbar比zxing快，而效果差不多，所以选择使用的是zbar。

<!-- more -->

# 使用

刚接触安卓不久，没有仔细研究jni编译，使用的是网上直接下载的Jar包和so文件，放入libs目录下，可以直接引用。可以在我的[github地址](https://github.com/paleomoon/android-zbar)下载。如果想自己编译，参考[二维码识别之Android完整编译Zbar](https://blog.csdn.net/yanzhenjie1003/article/details/71641368)。

# 图像数据格式

网上基本上使用的是安卓自带的摄像头，可以直接在回调函数获取数据：

```
PreviewCallback previewCb = new PreviewCallback() {
	public void onPreviewFrame(byte[] data, Camera camera) {
		Camera.Parameters parameters = camera.getParameters();
        Size size = parameters.getPreviewSize();
                 
        Image barcode = new Image(size.width, size.height, "Y800");
        barcode.setData(data);
        int result = scanner.scanImage(barcode);
    }
}
```

data就是每一帧图像的像素数据，YUV格式，对应zbar的"Y800"格式（实际使用发现二值图像素数据也可以）。

但是项目中我使用的是USB摄像头，根据网上找的USB摄像头例程SimpleWebCam获取到的是每一帧的Bitmap，然后我再从中获取图像处理中更常用的BGR像素数据。当然可以在USB摄像头例程中通过jni获取YUV数据，这样速度肯定更快。

zbar怎样使用BGR数据呢？百度搜索没有找到答案，然而用英文google第一条就找到答案（所以技术问题还是要多用google，远离百度）。zbar只支持扫描"Y800"和‘GRAY“格式的图像，但是Image可以导入多种类型的数据，最后转成支持的格式即可：

```
Image barcode = new Image(width, height, "BGR3"); //一般图像处理习惯使用BGR三通道数据
barcode.setData(data);
int result = scanner.scanImage(barcode.convert("Y800"));
```

再比如使用安卓API直接获取"ARGB"格式数据：

```
int[] data = new int[width*height];
bmp.getPixels(data, 0, width, 0, 0, width, height);
```

```
Image barcode = new Image(width, height, "ARGB");
barcode.setData(data);
int result = scanner.scanImage(barcode.convert("Y800"));
```

# 检测效果

一开始发现二维码识别效率不高，更改了参数后识别效果明显提升：

```
//网上大家使用的参数
//scanner.setConfig(0, Config.X_DENSITY, 3);
//scanner.setConfig(0, Config.Y_DENSITY, 3);

//效果提升的参数
scanner.setConfig(0, Config.ENABLE, 0);
scanner.setConfig(Symbol.QRCODE, Config.ENABLE, 1);
```

更改后的参数只识别二维码，不识别条形码。

# 参考

- [二维码识别之Android完整编译Zbar](https://blog.csdn.net/yanzhenjie1003/article/details/71641368)
- [Android开源库之使用ZBar开源库实现二维码功能](https://blog.csdn.net/super_spy/article/details/72783082)