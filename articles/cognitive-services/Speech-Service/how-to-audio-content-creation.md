---
title: 音频内容创建-语音服务
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
ms.openlocfilehash: 083580435c467a7d4b6a4cede0a821a2c271962f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589646"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>利用音频内容创建工具改善合成

[音频内容创建](https://aka.ms/audiocontentcreation)是一个在线工具，可用于为应用和产品自定义和微调 Microsoft 的文本到语音输出。 您可以使用此工具来微调公共和自定义语音以获取更准确的自然表达式，并在云中管理输出。

音频内容创建工具基于[语音合成标记语言（SSML）](speech-synthesis-markup.md)。 为了简化自定义和优化，音频内容创建使你能够以可视方式实时检查文本到语音输出。

## <a name="how-does-it-work"></a>工作原理

此图显示了微调文本到语音输出所需要执行的步骤。 使用以下链接详细了解每个步骤。

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [设置 Azure 帐户和语音资源](#set-up-your-azure-account-and-speech-resource)以开始工作。
2. 使用纯文本或 SSML 脚本[创建音频优化文件](#create-an-audio-tuning-file)。
3. 选择脚本内容的语音和语言。 音频内容创建包括[Microsoft 的文本到语音转换](language-support.md#text-to-speech)的所有声音。 您可以使用标准、神经或您自己的自定义语音。
   >[!NOTE]
   > "封闭访问" 适用于自定义的神经声音，这允许您创建类似于自然语音的高清晰声音。 有关更多详细信息，请参阅控制[过程](https://aka.ms/ignite2019/speech/ethics)。

4. 查看默认合成输出。 然后通过调整发音、中断、螺距、rate、intonation、语音样式等来改善输出。 有关选项的完整列表，请参阅[语音合成标记语言](speech-synthesis-markup.md)。 以下[视频](https://youtu.be/mUvf2NbfuYU)演示如何使用音频内容创建来微调语音输出。 
5. 保存并[导出优化的音频](#export-tuned-audio)。 在系统中保存优化轨后，可以继续工作，并在输出上循环访问。 如果对输出满意，可以使用导出功能创建音频创建任务。 你可以查看导出任务的状态，并下载用于应用和产品的输出。

## <a name="set-up-your-azure-account-and-speech-resource"></a>设置 Azure 帐户和语音资源

1. 若要使用音频内容创建，你必须有一个 Azure 帐户。 可以使用 Microsoft 帐户创建 Azure 帐户。 按照这些说明[设置 Azure 帐户](get-started.md#new-resource)。 
2. 为你的 Azure 帐户[创建语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource)。 请确保定价层设置为**S0**。 如果正在使用其中一个神经声音，请确保在[受支持的区域](regions.md#standard-and-neural-voices)中创建资源。
2. 获取 Azure 帐户和语音资源后，可以使用语音服务和访问[音频内容创建](https://aka.ms/audiocontentcreation)。
3. 选择需要使用的语音资源。 你还可以在此处创建新的语音资源。 
4. 你可以随时通过 "**设置**" 选项（位于顶部导航栏中）修改语音资源。

## <a name="create-an-audio-tuning-file"></a>创建音频优化文件

可以通过两种方式将内容引入音频内容创建工具。

**选项 1：**

1. 单击 "**新建文件**" 以创建新的音频优化文件。
2. 在编辑窗口中键入或粘贴您的内容。 每个文件的字符数最多为20000。 如果脚本的长度超过20000个字符，则可以使用选项2自动将内容拆分为多个文件。 
3. 别忘了保存。

**选项 2：**

1. 单击 "**上载**" 以导入一个或多个文本文件。 支持纯文本和 SSML。
2. 如果脚本文件超过20000个字符，请按段落、字符或正则表达式拆分文件。 
3. 上载文本文件时，请确保该文件满足这些要求。

   | Property | 值/注释 |
   |----------|---------------|
   | 文件格式 | 纯文本 (.txt)<br/> SSML 文本（.txt）<br/> 不支持 Zip 文件 |
   | 编码格式 | UTF-8 |
   | 文件名 | 每个文件必须具有唯一的名称。 不支持重复项。 |
   | 文本长度 | 文本文件的长度不能超过20000个字符。 |
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

查看音频输出并且对调整和调整满意后，可以导出音频。

1. 单击 "**导出**" 以创建音频创建任务。 建议**导出到音频库**，因为它支持长音频输出和完整的音频输出体验。 还可以直接将音频下载到本地磁盘，但只有前10分钟可用。 
2. 选择优化音频的输出格式。 下面提供了支持的格式和采样速率的列表。
3. 可以在 "**导出任务**" 选项卡上查看任务的状态。如果任务失败，请参阅详细信息页获取完整的报表。
4. 完成该任务后，可以在 "**音频库**" 选项卡上下载音频。
5. 单击“下载”  。 现在，你已准备好在你的应用或产品中使用自定义的优化音频。

### <a name="supported-audio-formats"></a>支持的音频格式

| 格式 | 16 kHz 采样率 | 24 kHz 采样率 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16 位 | riff-24khz-16 位 |
| mp3 | 16khz-128kbitrate-mp3 | 24khz-160kbitrate-mp3 |

## <a name="see-also"></a>另请参阅

* [长音频 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
