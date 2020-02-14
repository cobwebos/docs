---
title: 语音设备 SDK 文档
titleSuffix: Azure Cognitive Services
description: 发行说明提供了有关语音设备 SDK 的更新、增强功能、bug 修复和更改的日志。 本文随每个版本的语音设备 SDK 进行更新。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: c12aaea1dbc99a3f6db064e03b4b49e569f15194
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189054"
---
# <a name="release-notes-speech-devices-sdk"></a>发行说明：语音设备 SDK

以下部分列出了最新版本中的更改。

## <a name="speech-devices-sdk-190"></a>语音设备 SDK 1.9.0：

- 提供[URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) （Linux ARM64）的初始二进制文件。
- Roobo v1 现在使用适用于 Speech SDK 的 Maven
- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.9.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-170"></a>语音设备 SDK 1.7.0：

- 现在支持 Linux ARM。
- 提供[Roobo V2 DDK](https://aka.ms/sdsdk-download-roobov2)的初始二进制文件（Linux ARM64）。
- Windows 用户可以使用 `AudioConfig.fromDefaultMicrophoneInput()` 或 `AudioConfig.fromMicrophoneInput(deviceName)` 来指定要使用的麦克风。
- 库大小已优化。
- 支持使用相同的语音/意向识别器对象的多项识别。
- 修复在停止识别时发生的偶尔挂起。
- 示例应用现在包含一个示例参与者. properties 文件，用于演示文件的格式。
- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.7.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-160"></a>语音设备 SDK 1.6.0：

- 通过常见的[示例应用程序](https://aka.ms/sdsdk-download)在 Windows 和 Linux 上支持[Azure Kinect 深色](https://azure.microsoft.com/services/kinect-dk/)
- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.6.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-151"></a>语音设备 SDK 1.5.1：

- 在示例应用中包括[对话](conversation-transcription-service.md)脚本。
- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.5.1。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>语音设备 SDK 1.5.0 _： 2019-可能发布

- 语音设备 SDK 现已正式发布，不再是封闭预览版。
- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.5.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 新的关键字技术带来了重大的质量改进，请参阅重大更改。
- 用于改进远距离识别的新音频处理管道。

**重大更改**

- 由于新的关键字技术，所有关键字都必须在我们改进的关键字门户中重新创建。 若要从设备完全删除旧关键字，请卸载旧应用。
  - adb 卸载 coginitiveservices 的 sdsdkstarterapp。

## <a name="speech-devices-sdk-140-2019-apr-release"></a>语音设备 SDK 1.4.0： 2019-Apr 版本

- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.4.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>语音设备 SDK 1.3.1： 2019-三月发布

- 已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.3.1。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 已更新关键字处理，请参阅重大更改。
- 示例应用程序为语音识别和翻译添加了语言选项。

**重大更改**

- [安装关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已简化，它现在是应用程序的一部分，无需在设备上单独安装。
- 关键字识别已更改，并且支持两个事件。
  - `RecognizingKeyword,` 指示语音结果包含（未验证）的关键字文本。
  - `RecognizedKeyword`，指示关键字识别已完成识别给定的关键字。

## <a name="speech-devices-sdk-110-2018-nov-release"></a>语音设备 SDK 1.1.0： 2018-11 月版

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.1.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 远场语音识别准确性已通过我们增强的音频处理算法得到提高。
- 示例应用程序添加了中文语音识别支持。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>语音设备 SDK 1.0.1： 2018-10 月版

- 将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到了 1.0.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 我们改进的音频处理算法将提高语音识别的准确性
- 修复了一个连续识别音频会话 bug。

**重大更改**

- 该版本中推出了大量重大更改。 有关 API 的详细信息，请查看[此页](https://aka.ms/csspeech/breakingchanges_1_0_0)。
- KWS 模型文件与语音设备 SDK 1.0.1 不兼容。 将新的关键字文件写入设备后，会删除现有的关键字文件。

## <a name="speech-devices-sdk-050-2018-aug-release"></a>语音设备 SDK 0.5.0： 2018-8 月版

- 通过修复音频处理代码中的 Bug，改进了语音识别的准确性。
- 将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到了 0.5.0 版。 有关详细信息，请参阅其[发行说明](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>语音设备 SDK 0.2.12733： 2018-可能发布

认知服务语音设备 SDK 的第一个公共预览版本。
