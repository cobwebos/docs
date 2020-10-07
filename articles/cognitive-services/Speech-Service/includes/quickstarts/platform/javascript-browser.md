---
title: 快速入门：用于 JavaScript（浏览器）的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，设置可将 JavaScript（浏览器）与语音服务 SDK 配合使用的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: 2701b4cd17a132de07c031166bbe4cb1086227e9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324722"
---
本指南介绍如何安装可以在网页中使用的 JavaScript 版[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>新建网站文件夹

新建空文件夹。 如果要在 web 服务器上承载示例，请确保 web 服务器可访问文件夹。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>将 JavaScript 的语音 SDK 解压缩到文件夹

将语音 SDK 作为 [.zip 包](https://aka.ms/csspeech/jsbrowserpackage)下载，并将其解压缩到新建文件夹。 这会导致以下五个文件被解压缩：
* `microsoft.cognitiveservices.speech.sdk.bundle.js` 人工可读的语音 SDK 版本。
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` 用于调试 SDK 代码的映射文件。
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` 用于 TypeScript 的对象定义
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` 语音 SDK 的简化版。
* `speech-processor.js` 用于提高某些浏览器性能的代码。

## <a name="create-an-indexhtml-page"></a>创建 index.html 页面

在文件夹中创建名为 `index.html` 的新文件，使用文本编辑器打开此文件。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]