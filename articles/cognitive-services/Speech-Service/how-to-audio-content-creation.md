---
title: 音频内容创建 - 语音服务
titleSuffix: Azure Cognitive Services
description: 音频内容创建是一个在线工具，可用于为应用和产品自定义和微调 Microsoft 的文本到语音输出。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224581"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>使用音频内容创建工具改进合成

[音频内容创建](https://aka.ms/audiocontentcreation)是一个在线工具，可用于为应用和产品自定义和微调 Microsoft 的文本到语音输出。 可使用此工具来微调公共和自定义语音，以获取更准确的自然表达式，并在云中管理输出。

音频内容创建工具基于[语音合成标记语言 (SSML)](speech-synthesis-markup.md)。 为了简化自定义和优化，音频内容创建使你能够实时可视化地检查文本到语音输出。

请参阅 [视频教程](https://www.youtube.com/watch?v=O1wIJ7mts_w) ，了解如何创建音频内容。

## <a name="how-does-it-work"></a>工作原理

此图显示了微调文本到语音输出所需的步骤。 使用以下链接详细了解每个步骤。

![微调文本到语音输出所用步骤的图表。](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 首先[设置 Azure 帐户和语音资源](#set-up-your-azure-account-and-speech-resource)。
2. 使用纯文本或 SSML 脚本[创建音频优化文件](#create-an-audio-tuning-file)。
3. 选择脚本内容的语音和语言。 音频内容创建包括所有 [Microsoft 文本到语音转换](language-support.md#text-to-speech)。 可使用标准语音、神经语音或者你自己的自定义语音。
   >[!NOTE]
   > 管控访问可用于自定义神经语音，这允许创建类似于自然语音的高清语音。 有关详细信息，请参阅[管控过程](https://aka.ms/ignite2019/speech/ethics)。

4. 查看默认的合成输出。 然后通过调整发音、停顿、音调、速率、语调、语音风格等来改进输出。 有关选项的完整列表，请参阅[语音合成标记语言](speech-synthesis-markup.md)。 以下 [视频](https://youtu.be/mUvf2NbfuYU) 演示如何使用音频内容创建来微调语音输出。 
5. 保存并[导出优化音频](#export-tuned-audio)。 在系统中保存优化音轨后，可继续工作并迭代输出。 如果对输出满意，可使用导出功能创建音频创建任务。 可查看导出任务的状态，并下载用于应用和产品的输出。

## <a name="set-up-your-azure-account-and-speech-resource"></a>设置 Azure 帐户和语音资源

1. 若要使用音频内容创建，必须具有 Azure 帐户。 通过使用 Microsoft 帐户可创建 Azure 帐户。 按照这些说明[设置 Azure 帐户](get-started.md#new-resource)。 
2. 为 Azure 帐户[创建语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource)。 确保定价层设置为 **S0**。 如果正在使用其中一个神经语音，请确保在[支持的区域](regions.md#standard-and-neural-voices)中创建资源。
2. 获取 Azure 帐户和语音资源后，可以使用语音服务并访问[音频内容创建](https://aka.ms/audiocontentcreation)。
3. 选择需要处理的语音资源。 也可以在这里创建新的语音资源。 
4. 可以随时使用位于顶部导航栏中的“设置”选项来修改语音资源。

## <a name="create-an-audio-tuning-file"></a>创建音频优化文件

有两种方式可以将内容引入音频内容创建工具。

选项 1：

1. 单击“新建文件”以创建新的音频优化文件。
2. 在编辑窗口键入或粘贴内容。 每个文件的字符数最多为 20,000 个。 如果脚本的长度超过 20,000 个字符，则可以使用选项 2 将内容自动拆分为多个文件。 
3. 切勿忘记保存。

选项 2：

1. 单击“上传”导入一个或多个文本文件。 支持纯文本和 SSML。
2. 如果脚本文件超过 20,000 个字符，请按段落、字符或正则表达式拆分文件。 
3. 上传文本文件时，请确保该文件满足这些要求。

   | 属性 | 值/注释 |
   |----------|---------------|
   | 文件格式 | 纯文本 (.txt)<br/> SSML 文本 (.txt)<br/> 不支持 Zip 文件 |
   | 编码格式 | UTF-8 |
   | 文件名 | 每个文件必须拥有唯一的名称。 不支持重复项。 |
   | 文本长度 | 文本长度不得超过 20,000 个字符。 |
   | SSML 限制 | 每个 SSML 文件只能包含一条 SSML。 |

### <a name="plain-text-example"></a>纯文本示例

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML 文本示例

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>导出优化音频

查看音频输出并且对调整和优化都满意后，就可以导出音频。

1. 单击“导出”以创建音频创建任务。 建议选择“导出到音频库”，因为它支持长音频输出和完整的音频输出体验。 还可直接将音频下载到本地磁盘，但只有前 10 分钟可用。 
2. 选择优化音频的输出格式。 下面提供了支持格式和采样率的列表。
3. 可在“导出任务”选项卡上查看任务的状态。如果任务失败，请参阅详细信息页获取完整的报表。
4. 完成该任务后，可以在“音频库”选项卡上下载音频。
5. 单击“下载”。 现在，你可以在你的应用或产品中使用自定义的优化音频。

### <a name="supported-audio-formats"></a>支持的音频格式

| 格式 | 16 kHz 采样率 | 24 kHz 采样率 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>另请参阅

* [长音频 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
