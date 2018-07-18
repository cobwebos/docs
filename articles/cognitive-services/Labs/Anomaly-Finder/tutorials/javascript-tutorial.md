---
title: 异常情况检测 Javascript 应用 - Microsoft 认知服务 | Microsoft Docs
description: 介绍使用 Microsoft 认知服务中的异常情况检测 API 的 Javascript Web 应用。 将原始数据点发送到 API 并获得预期值和异常点。
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453163"
---
# <a name="anomaly-detection-javascript-application"></a>异常情况检测 Javascript 应用程序

介绍使用异常情况检测 REST API 检测异常的 Web 应用。 该示例使用订阅密钥将时序数据提交到异常情况检测 API，然后从 API 获取每个数据点的所有异常点和预期值。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

本教程采用简单的文本编辑器。

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>订阅异常情况检测并获取订阅密钥 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>获取并使用示例

本教程提供两种时序数据异常情况检测方案。 让我们开始吧。

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>下载教程项目

克隆[认知服务 JavaScript 异常情况检测教程](https://github.com/MicrosoftAnomalyDetection/javascript-sample)，或下载 .zip 文件并将其解压缩到空目录。

<a name="Step2"></a>
### <a name="run-the-example"></a>运行示例

可尝试通过两种方案运行该示例。
1. 将“订阅密钥”置于 anomalydetection.html 上检测功能上的“订阅密钥”字段中。
2. 放置异常情况检测 API 终结点，并验证“订阅区域”中使用的区域是否正确。
3. 在 Web 浏览器中打开 anomalydetection.html 文件。

**方案 1：检测每周的时序数据**
1. 在“时段”字段中，输入时间 7。 
2. 将示例数据替换为“时间点”字段中的每周时序数据点 (Json)，或直接使用示例数据。
3. 单击“异常情况检测”按钮，然后验证右侧“响应”文本框中的结果。

**方案 2：检测无时段的时序数据**
1. 留空“时段”字段，假设时序的时间段未知。
2. 使用与方案 1 相同的时序数据。
3. 单击“异常情况检测”按钮，然后验证右侧“响应”文本框中的“时段”字段。

<a name="Step3"></a>
### <a name="read-the-result"></a>读取结果

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>温故知新

现在应用程序已经在运行了。 我们来回顾一下示例应用是如何与认知服务技术集成的。 这样用户能更轻松地继续构建此应用或是使用 Microsoft 异常情况检测开发自己的应用。
此示例应用采用了异常情况检测 Restful API 终结点。
我们来看一看 anomalydetection.html 中的一个代码片段，回顾一下 Restful API 在示例应用程序中的用法。
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
