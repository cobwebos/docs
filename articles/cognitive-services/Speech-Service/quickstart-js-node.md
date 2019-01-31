---
title: 快速入门：识别语音，Node.js - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Node.js 的语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: c9f83368a540003e8bcc35282c664ec23e4ff068
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226496"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>快速入门：使用适用于 Node.js 的语音 SDK 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何创建使用认知服务语音 SDK 的 JavaScript 绑定以将语音转录为文本的 Node.js 项目。
应用程序基于 Microsoft [Cognitive Services Speech SDK](https://aka.ms/csspeech/npmpackage)。

## <a name="prerequisites"></a>先决条件

* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 当前版本的 [Node.js](https://nodejs.org)。

## <a name="create-a-new-project"></a>创建新项目

创建新文件夹并初始化该项目。

```sh
npm init -f
```

这将使用默认值初始化 package.json 文件。 稍后你可能需要编辑此文件。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

将语音 SDK 添加到 Node.js 项目。

```
npm install microsoft-cognitiveservices-speech-sdk
```

这将从 npmjs 下载并安装最新版本的语音 SDK 和任何需要的必备组件。 SDK 将安装在项目文件夹的 `node_modules` 目录中。

## <a name="use-the-speech-sdk"></a>使用语音 SDK

在文件夹中创建名为 `index.js` 的新文件，使用文本编辑器打开此文件。

> [!NOTE]
> 请注意，在 Node.js 中，语音 SDK 不支持麦克风或文件数据类型。 两者仅受浏览器支持。 请改用语音 SDK 的流接口，可通过 `AudioInputStream.createPushStream()` 或 `AudioInputStream.createPullStream()` 来实现。

本示例使用 `PushAudioInputStream` 接口。

添加以下 JavaScript 代码：

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>运行示例

若要启动该应用，请根据配置调整 `YourSubscriptionKey`、`YourServiceRegion` 和 `YourAudioFile.wav`。 然后可以通过调用以下命令来执行应用：

```sh
node index.js
```

该应用将使用提供的文件名触发识别，并在控制台上显示输出。

下面是更新订阅密钥并使用文件 `whatstheweatherlike.wav` 后运行 `index.js` 的示例输出。

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>通过 Visual Studio Code 安装并使用语音 SDK

也可以通过 Visual Studio Code 运行示例。 请按照以下步骤安装、打开和执行快速入门：

1. 启动 Visual Studio Code，单击“打开文件夹”，然后导航到快速入门文件夹

   ![“打开文件夹”的屏幕截图](media/sdk/qs-js-node-01-open_project.png)

1. 在 Visual Studio Code 中打开终端

   ![终端窗口的屏幕截图](media/sdk/qs-js-node-02_open_terminal.png)

1. 运行 npm 安装依赖项

   ![npm 安装的屏幕截图](media/sdk/qs-js-node-03-npm_install.png)

1. 现在可以打开 `index.js` 并设置断点

   ![第 16 行上存在断点的 index.js 的屏幕截图](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. 若要开始调试，请按 F5 或从菜单中选择“调试/开始调试”

   ![调试菜单的屏幕截图](media/sdk/qs-js-node-05-start_debugging.png)

1. 遇到断点时，可以检查调用栈和变量

   ![调试程序的屏幕截图](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 任何输出都将在调试控制台窗口中显示

   ![调试控制台的屏幕截图](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Node.js 示例](https://aka.ms/csspeech/samples)
