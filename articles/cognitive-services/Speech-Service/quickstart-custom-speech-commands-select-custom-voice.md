---
title: 快速入门：将自定义命令用于自定义语音（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将指定自定义命令应用程序的输出声音。
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872470"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>快速入门：将自定义命令用于自定义语音（预览）

在[前面的文章](./quickstart-custom-speech-commands-create-parameters.md)中，我们创建了一个新的自定义命令项目来响应带参数的命令。

在本文中，我们将为所创建的应用程序选择自定义输出声音。

## <a name="select-a-custom-voice"></a>选择自定义语音

1. 打开[我们之前创建](./quickstart-custom-speech-commands-create-parameters.md)的项目
1. 从左窗格中选择 "**设置**"
1. 从中间窗格中选择 "**自定义语音**"
1. 从表中选择所需的自定义或公共语音
1. 选择“保存” 

> [!div class="mx-imgBorder"]
> ![带参数的示例句子](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - 对于**公共语音**，**神经类型**仅适用于特定区域。 若要查看可用性，请参阅[按区域/终结点的标准和神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。
> - 对于**自定义**语音，可以从自定义语音项目页创建。 请参阅[自定义语音入门](./how-to-custom-voice.md)。

现在，应用程序将在选定的语音中做出响应，而不是默认的语音。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用 Speech SDK （预览版）连接到自定义命令应用程序](./quickstart-custom-speech-commands-speech-sdk.md)

