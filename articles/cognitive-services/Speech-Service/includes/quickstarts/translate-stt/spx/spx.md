---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806210"
---
## <a name="run-the-speech-cli"></a>运行语音 CLI

现在，可以运行语音 CLI，将语音转换为采用不同语言的文本。

在命令行中，更改为包含语音 CLI 二进制文件的目录，然后键入：

```bash
spx translate --microphone --target de-DE
```

语音 CLI 会将自然语言英语口语转换为采用德语输出的文本。
按 ENTER 停止该工具。

> [!NOTE]
> 语音 CLI 默认为英语。 你可以[从“语音转文本”表中](../../../../language-support.md)选择不同语言。
> 例如，添加 `--source ja-JP` 以识别日语语音。
