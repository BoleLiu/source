---
title: okhttp 的 POST、GET 请求
date: 2018-01-01 14:02:36
tags:
---
okhttp 是时下比较火的一个 http 框架，它支持 get 请求和 post 请求，支持基于 http 的文件上传和下载，支持加载图片，支持下载文件透明的 GZIP 压缩，支持响应缓存避免重复的网络请求，支持使用连接池来降低响应延迟问题。

最近项目上有许多涉及到 http 请求的地方，所以抽个时间总结下相关的用法，既能给自己沉淀一下，也可以帮助到一些初学者~

[okhttp 官网地址](http://square.github.io/okhttp/)

[okhttp github 地址](https://github.com/square/okhttp)

基本的配置方法可以看下上面的官网链接。因为在日常开发过程中最常用到的就是 POST 请求和 GET 请求，所以下面主要是整理一下这两种请求方式的使用姿势：

# HTTP GET

```java
OkHttpClient okHttpClient = new OkHttpClient();

Request request = new Request.Builder()
                .header("Authorization", AUTHORIZATION)
                .header("key", "value") // cookie、User-Agent 等
                .url(url)
                .build();

try {
    Response response = okHttpClient.newCall(request).execute();
    if (response.isSuccessful()) {
        return response.body().string();
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

可以通过 response.isSuccessful() 判断是否成功：

```java
Returns true if the code is in [200..300), which means the request was successfully received, understood, and accepted.
```

GET 请求中如果有 query 类型的参数，可以直接在构造 url 的时候进行相关 query 参数的赋值操作。

# HTTP POST

## HTTP POST JSON 数据

```java
private static final MediaType JSON = MediaType.parse("application/json; charset=utf-8");
OkHttpClient client = new OkHttpClient();

public String post(String url, String json) {
    // must have request body, even null
    RequestBody requestBody = RequestBody.create(JSON, json);

    Request request = new Request.Builder()
                .header("Authorization", API_KEY)
                .url(url)
                .post(requestBody)
                .build();
    try {
        Response response = client.newCall(request).execute();
        if (response.isSucessful()) {
            return response.body().string();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

## HTTP POST 表单上传

```java
OkHttpClient okHttpClient = new OkHttpClient();

RequestBody body = new FormBody.Builder()
    .add("key", "value")
    .add("key", "value")
    .build();

Request request = new Request.Builder()
    .url(url)
    .header("Authorization", AUTHORIZATION)
    .post(body)
    .build();

try {
    Response response = okHttpClient.newCall(request).execute();
    if (response.isSuccessful()) {
        return response.body().string();
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

## HTTP POST 提交分块请求

当上传的数据中包含文件的时候，比如上传图片等场景下，要使用这种写法。

```java
OkHttpClient client = new OkHttpClient();

 RequestBody fileBody = RequestBody.create(MediaType.parse("image/png"), file);

RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("label", label)
                .addFormDataPart("image_file", file.getName(), fileBody)
                .addFormDataPart("resolution", resolution)
                .build();

Request request = new Request.Builder()
                .url(url)
                .header("Authorization", AUTHORIZATION)
                .post(requestBody)
                .build();

try {
    Response response = client.newCall(request).execute();
    if (response.isSuccessful()) {
        return response.body().string();
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

# 同步与异步

在上述例子中，client.newCall(request).execute() 执行后就是在执行 HTTP 请求了，但是这个执行过程是同步的，如果在 Android 的主线程中操作会抛异常，所以 okhttp 也提供了异步的方法：

```java
OkHttpClient okHttpClient = new OkHttpClient();
Request request = new Request.Builder()
        .url(url)
        .build();
okHttpClient.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        e.printStackTrace();
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        if (response.isSuccessful()) {
            return response.body().string();
        }
    }
});
```

因为该操作是在异步线程中调用的，所以不能在回调里更新 UI，如果要在回调中去更新 UI 的话，可以调用 runOnUIThread() 的方式去更新。