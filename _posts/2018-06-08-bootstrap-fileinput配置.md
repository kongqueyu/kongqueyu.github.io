---
layout: post
title:  "bootstrap-fileinput配置"
categories: kongQueYu
tags:
author: kongQueYu
---

* content
{:toc}

bootstrap-fileinput是基于bootstrap的上传控件
## 基础code：
```js
    <script src="../../common/libs/bootstrap-fileinput/js/fileinput.min.js"></script>
    <script src="../../common/libs/bootstrap-fileinput/js/locales/zh.js"></script>
    <link rel="stylesheet" href="../../common/libs/bootstrap-fileinput/css/fileinput.min.css"/>
    function getFileInputOptions(imageUrl) {
        var imageFileOptions;
            if(imageUrl) {
                imageFileOptions = $.extend({
                    initialPreview : [ // 预览图片的设置
                        "<img src='" + imageUrl + "' class='file-preview-image' style='width:auto;height:auto;max-width:100%;max-height:100%;'> "]
                }, {
                    showPreview: true,
                    showUpload: false,
                    showRemove: true,
                    showCancel:false,
                    language: 'zh',
                    allowedPreviewTypes: ['image'],
                    allowedFileExtensions: ['jpg','jpeg', 'png', 'gif'],
                    maxFileSize: 10240,
                    msgFilesTooMany: "选择上传的文件数量({n}) 超过允许的最大数值{m}！",
                    layoutTemplates:{
                        actionDelete:''
                    }
                }
                );
            } else {
                imageFileOptions = {
                    showPreview: true,
                    showUpload: false,
                    showRemove: true,
                    language: 'zh',
                    allowedPreviewTypes: ['image'],
                    allowedFileExtensions: ['jpg','jpeg', 'png', 'gif'],
                    maxFileSize: 10240,
                    msgFilesTooMany: "选择上传的文件数量({n}) 超过允许的最大数值{m}！"
                };
            }
        return imageFileOptions;
    }
    <div style="width: 400px;">
        <input id="adImage" name="adImage" type="file" class="imageFile" src="">
    </div>
     $('input[class=imageFile]').each(function() {
        var imgUrl = $(this).attr('src');
        var imageFileOptions = getFileInputOptions(imgUrl);
        $(this).fileinput(imageFileOptions);
    });
```

## 配置
```javascript
        {
                 uploadUrl:"<%=basePath%>uploadFile",//上传的地址
                 uploadAsync:false, //默认异步上传
                 showUpload: false, //是否显示上传按钮,跟随文本框的那个
                 showRemove : true, //显示移除按钮,跟随文本框的那个
                 showCaption: true,//是否显示标题,就是那个文本框
                 showPreview : true, //是否显示预览,不写默认为true
                 dropZoneEnabled: false,//是否显示拖拽区域，默认不写为true，但是会占用很大区域
                 minImageWidth: 50, //图片的最小宽度
                 minImageHeight: 50,//图片的最小高度
                 maxImageWidth: 1000,//图片的最大宽度
                 maxImageHeight: 1000,//图片的最大高度
                 maxFileSize: 0,//单位为kb，如果为0表示不限制文件大小
                 minFileCount: 0,
                 maxFileCount: 4, //表示允许同时上传的最大文件个数
                 enctype: 'multipart/form-data',
                 validateInitialCount:true,
                 previewFileIcon: "<i class='glyphicon glyphicon-king'></i>",
                 msgFilesTooMany: "选择上传的文件数量({n}) 超过允许的最大数值{m}！",
                 allowedFileTypes: ['image','text'],//配置允许文件上传的类型
                 allowedPreviewTypes : [ 'image','text'],//配置所有的被预览文件类型
                 allowedPreviewMimeTypes : [ 'jpg', 'png', 'gif' ,'txt'],//控制被预览的所有mime类型
                 language : 'zh'
            }
```
## 疑问（如何去掉Bootstrap fileinput缩略图上面的上传删除按钮？）
```javascript
{
                showPreview: true,
                showUpload: false,
                showRemove: true,
                showCancel:false,
                language: 'zh',
                allowedPreviewTypes: ['image'],
                allowedFileExtensions: ['jpg','jpeg', 'png', 'gif'],
                maxFileSize: 10240,
                msgFilesTooMany: "选择上传的文件数量({n}) 超过允许的最大数值{m}！",
                layoutTemplates:{
                    actionDelete:''//将删除图片设置为空即可
                }
            }
```

