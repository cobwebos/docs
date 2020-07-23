---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806300"
---
## <a name="enable-microphone"></a>启用麦克风

插上并打开电脑麦克风，同时关闭任何可能会使用麦克风的应用。 某些计算机具有内置麦克风，其他计算机则需要配置蓝牙设备。

## <a name="run-the-speech-cli"></a>运行语音 CLI

现在，可以运行语音 CLI 来识别来自麦克风的语音。

1. 启动应用 - 在命令行中，更改为包含语音 CLI 二进制文件的目录，然后键入：
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > 语音 CLI 默认为英语。 你可以[从“语音转文本”表中](../../../../language-support.md)选择不同语言。
    > 例如，添加 `--source de-DE` 以识别德语语音。

2. 开始识别 - 向麦克风讲话。 随后可以看到字词会实时转录为文本。 如果停止说话一段时间，或者按 ctrl-C，语音 CLI 将停止。
