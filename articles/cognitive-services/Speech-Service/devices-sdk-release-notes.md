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
ms.date: 11/13/2019
ms.author: wellsi
ms.openlocfilehash: 3a74f3eb9aece3535f5505d69833a074c2dd0ed2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091202"
---
# <a name="release-notes-speech-devices-sdk"></a>发行说明：语音设备 SDK

以下部分列出了最新版本中的更改。

## <a name="speech-devices-sdk-170"></a>语音设备 SDK 1.7.0：

*   现在支持 Linux ARM。
*   提供了 Roobov2 的初始二进制文件（Linux ARM64）。
*   Windows 用户可以使用 AudioConfig. fromDefaultMicrophoneInput （）或 AudioConfig （deviceName）来指定要使用的麦克风。
*   库大小已优化。
*   支持使用相同的语音/意向识别器对象的多项识别。
*   修复在停止识别时发生的偶尔挂起。
*   示例应用现在包含一个示例参与者. properties 文件，用于演示文件的格式。
*   已将[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本1.7.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-160"></a>语音设备 SDK 1.6.0：

- 在 Windows 和 Linux 上支持 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)，提供常见的[示例应用程序](https://aka.ms/sdsdk-download)
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.6.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-151"></a>语音设备 SDK 1.5.1：

- 在示例应用中包括[对话听录](conversation-transcription-service.md)。
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.5.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>语音设备 SDK 1.5.0 _： 2019-可能发布

- 语音设备 SDK 现为 GA，不再是受限的预览版。
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.5.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 新的关键字技术带来了重大的质量改进，请参阅重大更改。
- 新的音频处理管道改进了远端域识别。

**重大更改**

- 由于新的关键字技术，所有关键字都必须在我们改进的关键字门户中重新创建。 若要从设备中完全删除旧的关键字，请卸载旧应用。
  - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>语音设备 SDK 1.4.0： 2019-Apr 版本

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.4.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>语音设备 SDK 1.3.1： 2019-三月发布

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.3.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 已更新关键字处理，请参阅重大更改。
- 示例应用程序添加了选择语言的功能，适用于语音识别和翻译。

**重大更改**

- [安装关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已简化，它现在是应用程序的一部分，无需在设备上单独安装。
- 关键字识别已更改，并且支持两个事件。
  - RecognizingKeyword，指示语音结果包含（未验证的）关键字文本。
  - RecognizedKeyword，指示关键字识别已完成识别给定关键字的过程。

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
