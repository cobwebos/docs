---
title: 情感 API Java for Android 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助用户通过认知服务中的 Java for Android 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 0e7d3991b195a83a8b87e306b3b34fbed2098581
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018020"
---
# <a name="emotion-api-java-for-android-quick-start"></a>情感 API Java for Android 快速入门

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松从视频提取见解，增强搜索结果等内容发现体验。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供了信息和代码示例，以帮助用户快速开始使用情感 API Android 客户端库中的[情感识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。 此示例演示了如何使用 Java 来识别人们所表达的情感。 

## <a name="prerequisites"></a>先决条件
* 在[此处](https://github.com/Microsoft/Cognitive-emotion-android)获取情感 API Java for Android SDK
* 在[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥

## <a name="recognize-emotions-java-for-android-example-request"></a>识别情感 Java for Android 示例请求

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>识别情感示例响应
成功的调用将返回一组按人脸矩形大小降序排列的人脸条目及其相关表情评分。 空响应指示未检测到任何人脸。 表情条目包含以下字段：
* faceRectangle - 图像中人脸的矩形位置。
* scores - 图像中每张人脸的表情得分。 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
