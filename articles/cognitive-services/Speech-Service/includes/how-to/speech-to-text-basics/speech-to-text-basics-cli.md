---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570567"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>从麦克风将语音转换为文本

插上并打开电脑麦克风，同时关闭任何可能会使用麦克风的应用。 某些计算机具有内置麦克风，其他计算机则需要配置蓝牙设备。

现在，可以运行语音 CLI 来识别来自麦克风的语音。 在命令行中，更改为包含语音 CLI 二进制文件的目录，然后运行以下命令。

```bash
spx recognize --microphone
```

> [!NOTE]
> 语音 CLI 默认为英语。 你可以[从“语音转文本”表中](../../../../language-support.md)选择不同语言。
> 例如，添加 `--source de-DE` 以识别德语语音。

对麦克风说话，随后可以看到字词会实时转录为文本。 如果停止说话一段时间，或者按 ctrl-C，语音 CLI 将停止。

## <a name="speech-to-text-from-audio-file"></a>从音频文件将语音转换为文本

语音 CLI 可以识别多种文件格式和自然语言的语音。 在此示例中，可以使用包含英语语音的 WAV 文件（16kHz 或 8kHz，16 位，mono PCM）。 如果需要快速示例，请下载 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> 文件，并将其复制到语音 CLI 二进制文件所在的目录中。

现在可以运行语音 CLI 来识别音频文件中找到的语音，方法是运行以下命令。

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> 语音 CLI 默认为英语。 你可以[从“语音转文本”表中](../../../../language-support.md)选择不同语言。
> 例如，添加 `--source de-DE` 以识别德语语音。

语音 CLI 将在屏幕上显示语音的文本转录。