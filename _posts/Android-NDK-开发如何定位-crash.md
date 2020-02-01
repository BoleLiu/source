---
title: Android NDK 开发如何定位 crash
date: 2017-11-23 11:39:20
tags: Android 开发学习
---

```java
11-23 14:34:16.635 725-725/? A/DEBUG: *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
11-23 14:34:16.635 725-725/? A/DEBUG: Build fingerprint: 'Xiaomi/capricorn/capricorn:6.0.1/MXB48T/V8.5.3.0.MAGCNED:user/release-keys'
11-23 14:34:16.635 725-725/? A/DEBUG: Revision: '0'
11-23 14:34:16.635 725-725/? A/DEBUG: ABI: 'arm'
11-23 14:34:16.635 725-725/? A/DEBUG: pid: 8308, tid: 8853, name: HWAudioEncoder  >>> com.qiniu.pili.droid.shortvideo.demo <<<
11-23 14:34:16.635 725-725/? A/DEBUG: signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x208100
11-23 14:34:16.641 1357-2559/? I/Timeline: Timeline: App_transition_ready time:62206750
11-23 14:34:16.642 1357-2559/? I/Timeline: Timeline: App_transition_ready time:62206751
11-23 14:34:16.644 725-725/? A/DEBUG:     r0 de380390  r1 00000000  r2 00000008  r3 de3803d8
11-23 14:34:16.644 725-725/? A/DEBUG:     r4 e9d8c4f0  r5 00000000  r6 df67d000  r7 de380428
11-23 14:34:16.644 725-725/? A/DEBUG:     r8 00000023  r9 de380390  sl 00208100  fp 017e5d69
11-23 14:34:16.644 725-725/? A/DEBUG:     ip 00000000  sp de380380  lr ddcb3b8f  pc ddcb3b96  cpsr 40070030
11-23 14:34:16.649 725-725/? A/DEBUG: backtrace:
11-23 14:34:16.649 725-725/? A/DEBUG:     #00 pc 0000ab96  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/lib/arm/libpldroid_shortvideo_core.so (muxer_write_audio_frame+73)
11-23 14:34:16.649 725-725/? A/DEBUG:     #01 pc 0000bfeb  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/lib/arm/libpldroid_shortvideo_core.so
11-23 14:34:16.649 725-725/? A/DEBUG:     #02 pc 00f3e7ad  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (int com.qiniu.pili.droid.shortvideo.muxer.FFMP4Muxer.writeAudioFrame(long, java.nio.ByteBuffer, int, long, int)+152)
11-23 14:34:16.649 725-725/? A/DEBUG:     #03 pc 00f3ec75  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.muxer.FFMP4Muxer.a(int, java.nio.ByteBuffer, android.media.MediaCodec$BufferInfo)+896)
11-23 14:34:16.649 725-725/? A/DEBUG:     #04 pc 00f40cef  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.muxer.a.a(int, java.nio.ByteBuffer, android.media.MediaCodec$BufferInfo)+82)
11-23 14:34:16.649 725-725/? A/DEBUG:     #05 pc 00f4108d  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.muxer.a.b(java.nio.ByteBuffer, android.media.MediaCodec$BufferInfo)+88)
11-23 14:34:16.649 725-725/? A/DEBUG:     #06 pc 00f43fcd  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.process.a.a$4.a(java.nio.ByteBuffer, android.media.MediaCodec$BufferInfo)+256)
11-23 14:34:16.649 725-725/? A/DEBUG:     #07 pc 00ec0a1f  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.d.c.i()+2970)
11-23 14:34:16.649 725-725/? A/DEBUG:     #08 pc 00ec0d67  /data/app/com.qiniu.pili.droid.shortvideo.demo-2/oat/arm/base.odex (offset 0x724000) (void com.qiniu.pili.droid.shortvideo.d.c.run()+186)
11-23 14:34:16.649 725-725/? A/DEBUG:     #09 pc 738fb139  /data/dalvik-cache/arm/system@framework@boot.oat (offset 0x24bc000)
```