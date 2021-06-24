---
title: IOS 视频播放报错"AVFoundationErrorDomain code:-11850/-11828"
date: 2021-04-13 15:57:07
tags:
---

## IOS 视频播放报错"AVFoundationErrorDomain code:-11850/-11828"

#### 问题现象

- IOS 播放 video 报错，Andriod 播放正常

#### 释义

- -11850:服务端不支持 byte 请求
- -11850:服务端支持 byte 请求，响应参数错误

![ios -11850](11850.jpg)
![ios -11828](11828.jpg)

#### 问题原因

-IOS 视频请求会先发送请求 header 带有 Range:bytes=0-1 的字节请求，期望结果为视频流中的一个字节，http 状态码返回 206，例如
请求

```
curl -H "Range: bytes=0-1" http://127.0.0.1:8180/test.mp4
```

响应
![ios -11828](postman.png)

#### 解决方案

java 配置

```
/**
     * 下载文件
     */
    @GetMapping("/ocstream")
    public void downloadCooperationSuiteFile1(@RequestParam String fileId,
                                             HttpServletResponse response,
                                             HttpServletRequest request) throws IOException {

        try {
            CooperationFileInfo info = fileService.queryFileInfo(fileId);
            String downloadUrl= info.getDownloadUrl();

            InputStream inputStream = fileService.downloadFileStream(downloadUrl);
            ServletOutputStream outputStream = response.getOutputStream();

            response.setHeader("Content-disposition",
                    String.format("attachment; filename=\"%s\"", FileDownloadNameFormatUtil.encodingFileName(info.getName(), request)));
            Long length =  info.getSize();
            int start = 0;
            int end = Math.toIntExact(length)- 1;
            String range = request.getHeader("Range");
//            Matcher matcher = RANGE_PATTERN.matcher(range);
            if(!StringUtils.isEmpty(range)){
//                response.reset();
                if (range.startsWith("bytes=")) {
                    range = range.substring("bytes=".length());
                    String[] index= range.split("-");
                    start = Integer.parseInt(index[0]);
                    start = start < 0 ? 0 : start;
                    end =   Integer.parseInt(index[1]);
                    int  contentLength =end +1-start;
//                    response.setHeader("Content-Range","bytes "+ range +"/"+total);
                    response.setHeader("Content-Range",
                            String.format("bytes %s-%s/%s", start, end, length));
                    response.setStatus(HttpServletResponse.SC_PARTIAL_CONTENT);
                    response.setHeader("Content-Length", String.format("%s", contentLength));
                    Calendar cal=Calendar.getInstance();
                    cal.add(Calendar.DATE,-1);
                    Date nowTime = cal.getTime
```

#### 参考文章

- [http 请求头 range](http://www.javashuo.com/article/p-kdnicmse-hx.html)
- [node 静态服务器断点续传实现](https://blog.csdn.net/weixin_33755554/article/details/88055335)
- [AVPlayer fails to play video sometimes](https://stackoverflow.com/questions/33823411/avplayer-fails-to-play-video-sometimes)
- [XMLHTTPRequest Byte Range Request](https://stackoverflow.com/questions/26252896/xmlhttprequest-byte-range-request)
- [HTTP 请求头 Range 信息](http://www.cocoachina.com/articles/51686)
