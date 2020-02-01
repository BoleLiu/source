---
title: OpenGL学习总结
date: 2017-07-13 23:09:08
tags:
---
## 如何判断一个手机是否支持 OpenGL 2.0
```
final ActivityManager activityManager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
final ConfigurationInfo configurationInfo = activityManager.getDeviceConfigurationInfo();
final boolean supportsEs2 = configurationInfo.reqGlEsVersion >= 0x20000;
```
首先，通过 activityManager 获取 ConfigurationInfo，然后访问 reqGlEsVersion 看其是否大于等于 0x20000，大即支持，否则不支持。

## OpenGL 中的变量
### Uniform
Uniform 是一种从 CPU 中的应用向 GPU 中的着色器发送数据的一种方式，