---
title: 使用七牛播放SDK进行播放地址的切换
date: 2017-03-20 15:34:43
categories: Android
tags: android
---
首先，整体上了解一下七牛的播放端SDK：
七牛播放端SDK提供了三种视频播放器以及一个音频播放器，在这三种视频播放器中，PLVideoView和PLTextureView是七牛封装好的，接口比较简单，使用方便，其中PLVideoView是基于SurfaceView的控件，PLTextureView是基于TextureView的控件，而PLMediaPlayer类似于MediaPlayer，提供了播放器的核心功能，但是使用起来比较复杂，问题也容易多些。

接下来，针对七牛播放器SDK的三种视频播放器的播放地址的切换，进行如下总结：
## PLVideoView & PLTextureView
切换代码如下：
```java
mVideoView.setVideoPath(mVideoPath);
mVideoView.start();
```
也就是说直接切换地址，再重新start就可以了。不需要再prepare，prepare等操作都封装在里面了。

## PLMediaPlayer
PLMediaPlayer不是一个View控件，因此，使用它播放视频的话需要自己在布局文件中定义一个SurfaceView或者TextureView的控件，然后将Surface或者SurfaceHolder传递给PLMediaPlayer。

使用PLMediaPlayer进行播放地址切换的时候比较复杂。首先，要知道PLMediaPlayer是通过setDataSource方法进行播放地址的设置的，而调用setDataSource之前要调用reset方法对PLMediaPlayer进行重置，否则就会报出IllegalStateException的异常。至于为什么会报出这个异常，是因为PLMediaPlayer需要在IDLE的状态下才能调用setDataSource方法，而调用reset才会进入IDLE状态。想了解更多可以参考[这篇文章](http://loody.github.io/2016/05/22/Android-MediaPlayer%E7%8A%B6%E6%80%81%E6%9C%BA/)。其次，进入IDLE状态后需要进行重新的初始化。因此，切换播放地址的代码如下：
```java
public void changePlayUrl() {
    mVideoPath = "new path"
    mMediaPlayer.stop();
    mMediaPlayer.reset();
    mMediaPlayer = null;
    prepare();
}

private void prepare() {

        if (mMediaPlayer != null) {
            mMediaPlayer.setDisplay(mSurfaceView.getHolder());
            if (!mIsLiveStreaming) {
                mMediaPlayer.seekTo(mMediaPlayer.getCurrentPosition());
            }
            return;
        }

        try {
            mMediaPlayer = new PLMediaPlayer(this, mAVOptions);

            mMediaPlayer.setOnPreparedListener(mOnPreparedListener);
            mMediaPlayer.setOnVideoSizeChangedListener(mOnVideoSizeChangedListener);
            mMediaPlayer.setOnCompletionListener(mOnCompletionListener);
            mMediaPlayer.setOnErrorListener(mOnErrorListener);
            mMediaPlayer.setOnInfoListener(mOnInfoListener);
            mMediaPlayer.setOnBufferingUpdateListener(mOnBufferingUpdateListener);

            // set replay if completed
            // mMediaPlayer.setLooping(true);

            mMediaPlayer.setWakeMode(getApplicationContext(), PowerManager.PARTIAL_WAKE_LOCK);

            mMediaPlayer.setDataSource(mVideoPath);
            mMediaPlayer.setDisplay(mSurfaceView.getHolder());
            mMediaPlayer.prepareAsync();
        } catch (UnsatisfiedLinkError e) {
            e.printStackTrace();
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        } catch (IllegalStateException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

上面就是使用七牛播放端SDK进行地址切换的方法，希望可以帮到大家。

