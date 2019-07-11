---
title: 快速入门：识别语音，Node.js - 语音服务
titleSuffix: Azure Cognitive Services
description: 参考本指南使用适用于 Node.js 的语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605129"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>快速入门：使用适用于 Node.js 的语音 SDK 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用 Azure 认知服务语音 SDK 的 JavaScript 绑定创建一个 Node.js 项目，以将语音转录为文本。
该应用程序基于[适用于 JavaScript 的语音 SDK](https://aka.ms/csspeech/npmpackage)。

## <a name="prerequisites"></a>先决条件

* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 当前版本的 [Node.js](https://nodejs.org)。

## <a name="create-a-new-project"></a>创建新项目

创建新文件夹并初始化该项目：

```sh
npm init -f
```

此命令使用默认值初始化 **package.json** 文件。 稍后可能需要编辑此文件。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

将语音 SDK 添加到 Node.js 项目：

```
npm install microsoft-cognitiveservices-speech-sdk
```

此命令从 **npmjs** 下载并安装最新版本的语音 SDK 和全部所需的必备组件。 SDK 将安装在项目文件夹中的 `node_modules` 目录内。

## <a name="use-the-speech-sdk"></a>使用语音 SDK

在文件夹中创建名为 `index.js` 的新文件，使用文本编辑器打开此文件。

> [!NOTE]
> 在 Node.js 中，语音 SDK 不支持麦克风或“文件”数据类型。  两者仅受浏览器支持。 请改用语音 SDK 的**流**接口，可通过 `AudioInputStream.createPushStream()` 或 `AudioInputStream.createPullStream()` 来实现。

本示例使用 `PushAudioInputStream` 接口。

添加以下 JavaScript 代码：

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>运行示例

若要打开应用，请根据配置调整 `YourSubscriptionKey`、`YourServiceRegion` 和 `YourAudioFile.wav`。 然后调用以下命令运行应用：

```sh
node index.js
```

此命令使用提供的文件名触发识别。 然后在控制台上显示输出。

此示例是在更新订阅密钥并使用文件 `whatstheweatherlike.wav` 后运行 `index.js` 时返回的输出：

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

也可以通过 Visual Studio Code 运行示例。 遵循以下步骤安装、打开并运行快速入门项目：

1. 启动 Visual Studio Code。 选择“打开文件夹”。  然后浏览到 quickstart 文件夹。

   ![打开文件夹](media/sdk/qs-js-node-01-open_project.png)

1. 在 Visual Studio Code 中打开终端。

   ![终端窗口](media/sdk/qs-js-node-02_open_terminal.png)

1. 运行 `npm` 以安装依赖项。

   ![npm install](media/sdk/qs-js-node-03-npm_install.png)

1. 现在可以打开 `index.js` 并设置断点。

   ![在第 16 行包含断点的 index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. 若要开始调试，请按 F5 或从菜单中选择“调试/开始调试”。 

   ![调试菜单](media/sdk/qs-js-node-05-start_debugging.png)

1. 命中断点时，可以检查调用堆栈和变量。

   ![调试程序](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 所有输出将显示在调试控制台窗口中。

   ![调试控制台](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Node.js 示例](https://aka.ms/csspeech/samples)
