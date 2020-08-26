---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170112"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>将语音合成到扬声器

现在，可以运行语音 CLI，以从文本合成语音。 在命令行中，更改为包含语音 CLI 二进制文件的目录。 然后，运行以下命令。

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

语音 CLI 将通过计算机扬声器生成英语的自然语言。

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

运行以下命令，将扬声器的输出更改为 `.wav` 文件。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

语音 CLI 将采用英文向 `greetings.wav` 音频文件生成自然语言。
在 Windows 中，输入 `start greetings.wav` 可以播放音频文件。