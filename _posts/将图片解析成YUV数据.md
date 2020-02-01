---
title: 将图片解析成YUV数据
date: 2017-03-20 15:04:33
categories: 多媒体
tags: 多媒体处理
---
最近在做图片推流的一个demo，涉及到了将图片解析成YUV原始数据进行推流，故参照网上的资料做了这样的一个总结。
首先，需要先了解一下RGB以及YUV数据的存储方式。
## RGB
关于什么是RGB就不再在这里赘述了，这里主要说一些RGB的存储格式：
* RGB565：每个像素用16位表示，RGB分量分别使用5位、6位、5位
* RGB555：每个像素用16位表示，RGB分量都是用5位，剩下1位不用
* RGB24： 每个像素用24位表示，RGB各占8位
* RGB32： 每个像素用32位表示，RGB各占8位，剩下8位用作Alpha通道或者不用
* ARGB32：每个像素用32位表示，RGB各占8位，剩下8位表示Alpha通道值

这个就是RGB格式的存储方式，这里我们要用到的就是ARGB32的这种。
## YUV数据
YUV的数据存储方式有两大类：planar和packed。

在采样方式上有三种：YUV4:4:4，YUV4:2:2，YUV4:2:0

其具体的介绍可以从下面这篇文章做一个大致的了解：
http://blog.csdn.net/yi412/article/details/50470250
同时，这篇文章中也介绍了将RGB转化成YUV和YCbCr的算法，我们就用这个算法来实现。

## 将图片解析成YUV数据并推流
因为Android camera preview默认格式为NV21的，所以为了保证推流，就将图片解析成NV21的数据。
根据上述的YUV格式所链接的那篇文章，我们知道了如何将具体代码如下：

```java

byte [] getNV21(int inputWidth, int inputHeight, Bitmap scaled) {

        int [] argb = new int[inputWidth * inputHeight];

        //该方法会返回一个ARGB格式的数组来存放像素文件
        scaled.getPixels(argb, 0, inputWidth, 0, 0, inputWidth, inputHeight);

        byte [] yuv = new byte[inputWidth*inputHeight*3/2];
        encodeYUV420SP(yuv, argb, inputWidth, inputHeight);

        scaled.recycle();

        return yuv;
    }

    void encodeYUV420SP(byte[] yuv420sp, int[] argb, int width, int height) {
        final int frameSize = width * height;

        int yIndex = 0;
        int uvIndex = frameSize;

        int a, R, G, B, Y, U, V;
        int index = 0;
        for (int j = 0; j < height; j++) {
            for (int i = 0; i < width; i++) {

                // 通过与操作以及移位的方式获取A、R、G、B等分量
                a = (argb[index] & 0xff000000) >> 24; 
                R = (argb[index] & 0xff0000) >> 16;
                G = (argb[index] & 0xff00) >> 8;
                B = (argb[index] & 0xff) >> 0;

                // well known RGB to YUV algorithm
                Y = ( (  66 * R + 129 * G +  25 * B + 128) >> 8) +  16;
                U = ( ( -38 * R -  74 * G + 112 * B + 128) >> 8) + 128;
                V = ( ( 112 * R -  94 * G -  18 * B + 128) >> 8) + 128;

                // NV21 has a plane of Y and interleaved planes of VU each sampled by a factor of 2
                //    meaning for every 4 Y pixels there are 1 V and 1 U.  Note the sampling is every other
                //    pixel AND every other scanline.
                yuv420sp[yIndex++] = (byte) ((Y < 0) ? 0 : ((Y > 255) ? 255 : Y));
                //
                if (j % 2 == 0 && index % 2 == 0) {
                    yuv420sp[uvIndex++] = (byte)((V<0) ? 0 : ((V > 255) ? 255 : V));
                    yuv420sp[uvIndex++] = (byte)((U<0) ? 0 : ((U > 255) ? 255 : U));
                }

                index ++;
            }
        }
    }
```
就这样，我们实现了将RGB格式的数据转化成NV21格式的数据，接下来就可以通过这个NV21数据来进行推流或者其他操作了。

[参考文章](http://stackoverflow.com/questions/5960247/convert-bitmap-array-to-yuv-ycbcr-nv21)