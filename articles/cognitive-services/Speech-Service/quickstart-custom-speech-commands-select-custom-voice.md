---
title: 快速入门：将自定义命令与自定义语音（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，您将指定自定义命令应用程序的输出语音。
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: b0ecbc4dc030fa8e7fbe362c1304c3c97278bdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456406"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>快速入门：使用自定义命令与自定义语音（预览）

在[上一篇文章中](./quickstart-custom-speech-commands-create-parameters.md)，我们创建了一个新的自定义命令项目来响应具有参数的命令。

在本文中，我们将为创建的应用程序选择自定义输出语音。

## <a name="select-a-custom-voice"></a>选择自定义语音

1. 打开[我们以前创建](./quickstart-custom-speech-commands-create-parameters.md)的项目
1. 从左侧窗格中选择 **"设置"**
1. 从中间窗格中选择 **"自定义语音**"
1. 从表中选择所需的自定义或公共语音
1. 选择 **"保存"**

> [!div class="mx-imgBorder"]
> ![具有参数的句子示例](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> 可以从自定义语音项目页面创建自定义语音。 选择**语音工作室**链接，然后**自定义语音**开始。

现在，应用程序将以所选语音而不是默认语音进行响应。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用语音 SDK 连接到自定义命令应用程序（预览版）](./quickstart-custom-speech-commands-speech-sdk.md)

