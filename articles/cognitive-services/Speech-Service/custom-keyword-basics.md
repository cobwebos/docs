---
title: 创建自定义关键字-语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终 (或短语) 侦听关键字。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 188824b9c43387cf106a3cdd53a09fd6bcb198b8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360661"
---
# <a name="custom-keyword-basics"></a>自定义关键字基础知识

本文介绍如何使用 Speech Studio 和 Speech SDK 处理自定义关键字。 关键字是允许激活语音的单词或短语。 使用 Speech Studio 创建关键字模型，然后在应用程序中导出用于语音 SDK 的模型文件。

## <a name="prerequisites"></a>先决条件

本文中的步骤需要语音订阅和语音 SDK。 如果还没有订阅，请 [免费试用语音服务](overview.md#try-the-speech-service-for-free)。 若要获取 SDK，请参阅适用于你的平台的 [安装指南](quickstarts/setup-platform.md) 。

## <a name="create-a-keyword-in-speech-studio"></a>在 Speech Studio 中创建关键字

你需要先使用[Speech Studio](https://aka.ms/sdsdk-speechportal)上的 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页创建关键字，然后才能使用自定义关键字。 提供关键字后，它会生成一个 `.table` 可与语音 SDK 一起使用的文件。

> [!IMPORTANT]
> 自定义关键字模型和生成的 `.table` 文件 **只能** 在 Speech Studio 中创建。
> 不能从 SDK 或 REST 调用创建自定义关键字。

1. 转到 [Speech Studio](https://aka.ms/sdsdk-speechportal) 并 **登录** ，如果还没有语音订阅，请选择 " [**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在 " [自定义关键字](https://aka.ms/sdsdk-wakewordportal) " 页上，创建一个 **新项目**。 

1. 输入 **名称**和可选 **描述**，并选择语言。 每种语言都需要一个项目，并且支持目前仅限于该 `en-US` 语言。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择你的项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要创建新的关键字模型，请单击 " **训练模型**"。

1. 输入模型的 **名称** 、可选 **描述**和所选 **关键字** ，然后单击 " **下一步**"。 请参阅有关选择有效关键字的 [准则](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) 。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 门户为关键字创建候选发音。 通过单击 "播放" 按钮来倾听每个候选项，并删除任何不正确的发音旁边的检查。 仅选中好发音后，单击 " **训练** " 开始生成关键字模型。 

    ![查看关键字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型最多可能需要30分钟时间。 模型完成后，关键字列表将从 **处理** 更改为 **成功** 。 然后，你可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 下载的文件是 `.zip` 存档文件。 解压缩存档，并看到一个扩展名为的文件 `.table` 。 这是你在下一部分中与 SDK 一起使用的文件，因此请务必记下其路径。 文件名镜像关键字名称，例如，关键字 **激活设备** 具有文件名 `Activate_device.table` 。

## <a name="use-a-keyword-model-with-the-sdk"></a>结合使用关键字模型和 SDK

首先，使用静态函数加载关键字模型文件 `FromFile()` ，该函数将返回 `KeywordRecognitionModel` 。 使用 `.table` 从 Speech Studio 下载的文件的路径。 此外，你可以 `AudioConfig` 使用默认麦克风创建，然后使用音频配置实例化新的 `KeywordRecognizer` 。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下来，通过一次调用来运行关键字识别， `RecognizeOnceAsync()` 方法是传递模型对象。 这会启动一个关键字识别会话，此会话将一直持续到识别关键字为止。 因此，通常在多线程应用程序中使用此设计模式，或在可能会无限期等待唤醒字词的使用情况下使用此设计模式。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此处显示的示例使用本地关键字识别，因为它不需要 `SpeechConfig` 用于身份验证上下文的对象，也不会与后端联系。 但是，可以 [使用连续后端连接](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)来运行关键字识别和验证。

## <a name="next-steps"></a>后续步骤

通过 [语音设备 SDK 快速入门](https://aka.ms/sdsdk-quickstart)测试自定义关键字。
