---
title: 项目 URL 预览的 JavaScript 快速入门 - Microsoft 认知服务 | Microsoft Docs
description: 可快速开始使用 Azure 上的 Microsoft 认知服务中的必应 URL 预览 API 的脚本示例。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366275"
---
# <a name="url-preview-in-javascript"></a>JavaScript 中的 URL 预览 

以下单页应用程序使用 JavaScript 创建 SwiftKey 站点的 URL 预览：https://swiftkey.com/en。 

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://aka.ms/answersearchsubscription)免费试用版的访问密钥

## <a name="code-scenario"></a>代码方案
下面的 javascript 示例包括用户在其中输入 URL 以进行预览的文本框输入对象。  当用户单击“预览”按钮时，onclick 方法会路由到 `getPreview`，代码在其中会生成对 UrlPreview 终结点的 Web 请求。

该代码会创建 XMLHttpRequest，添加 Ocp-Apim-Subscription-Key 标头和密钥，并发送请求。  它会添加异步事件处理程序来处理该响应。

如果成功返回响应，该处理程序会将响应的 JSON 文本分配到页面上的 `demo` 段落。 其他响应元素设置为以下供显示的段落。

**原始 JSON 响应**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**正在运行的演示**

![JavaScript URL 预览示例](./media/java-script-demo.png)

## <a name="running-the-application"></a>运行应用程序

运行应用程序：

1. 使用订阅的有效访问密钥替换 `YOUR-SUBSCRIPTION-KEY` 值。
2. 将 HTML 和脚本保存到扩展名为 .html 的文件中。
3. 在浏览器中运行网页。
4. 使用现有 URL，或在文本框中输入其他 URL。
5. 单击“预览”按钮。

源代码：

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>后续步骤
- [C# 快速入门](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)