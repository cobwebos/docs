---
title: 快速入门：使用认知服务语音 SDK 在浏览器的 JavaScript 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在浏览器的 JavaScript 中识别语音
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 2667d398141b53c87328eec7b608c4f613c15ea4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340267"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>快速入门：使用认知服务语音 SDK 在浏览器的 JavaScript 中识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何创建使用认知服务语音 SDK 的 JavaScript 绑定以将语音转录为文本的网站。
应用程序基于 Microsoft 认知服务语音 SDK（[下载版本 1.0.1](https://aka.ms/csspeech/jsbrowserpackage)）。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 配有可正常工作的麦克风的电脑或 Mac。
* 文本编辑器。
* Chrome 或 Microsoft Edge 的当前版本。
* （可选）支持承载 PHP 脚本的 web 服务器。

## <a name="create-a-new-website-folder"></a>新建网站文件夹

新建空文件夹。 如果要在 web 服务器上承载示例，请确保 web 服务器可访问文件夹。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>将 JavaScript 的语音 SDK 解压缩到文件夹

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

将语音 SDK 作为 [.zip 包](https://aka.ms/csspeech/jsbrowserpackage)下载，并将其解压缩到新建文件夹。 这应该会解压缩两个文件，即 `microsoft.cognitiveservices.speech.sdk.bundle.js` 和 `microsoft.cognitiveservices.speech.sdk.bundle.js.map`。
后一个文件是可选的，可用于调试到 SDK 代码（如有必要）。

## <a name="create-an-indexhtml-page"></a>创建 index.html 页面

在文件夹中创建名为 `index.html` 的新文件，使用文本编辑器打开此文件。

1. 创建以下 HTML 框架：

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. 将以下 UI 代码添加到文件，以下是第一个注释：

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. 添加对语音 SDK 的引用

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. 连接“识别”按钮、识别结果和 UI 代码定义的订阅相关字段的处理程序：

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>创建令牌源（可选）

如果要在 web 服务器上承载网页，可以为演示应用程序提供令牌源。
这样一来，订阅密钥永远不会离开服务器，并且用户可以在不输入任何授权代码的情况下使用语音功能。

1. 创建名为 `token.php` 的新文件。 此示例假设 web 服务器支持 PHP 脚本语言。 输入以下代码：

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. 编辑 `index.html` 文件，将以下代码添加到文件：

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> 授权令牌仅具有有限的生存期。
> 此简化示例不显示如何自动刷新授权令牌。 作为用户，你可以手动重载页面或点击 F5 刷新。

## <a name="build-and-run-the-sample-locally"></a>在本地生成和运行示例

要启动应用，双击 index.html 文件或使用你喜欢的 web 浏览器打开 index.html。 随即显示的简单 GUI 允许你输入订阅密钥和[区域](regions.md)以及使用麦克风触发识别。

## <a name="build-and-run-the-sample-via-a-web-server"></a>通过 web 服务器生成并运行示例

要启动应用，打开你最喜欢的 web 浏览器，将其指向承载文件夹的公共 URL，输入你的[区域](regions.md)，使用麦克风触发识别。 配置后，它将获取令牌源中的令牌。

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/js-browser` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取我们的示例](speech-sdk.md#get-the-samples)
