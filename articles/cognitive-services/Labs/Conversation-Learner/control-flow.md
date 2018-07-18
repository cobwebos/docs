---
title: 对话学习器控制流 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解对话学习器控制流。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366245"
---
## <a name="control-flow"></a>控制流

本文介绍对话学习器 (CL) 的控制流，如下方图表所示。

![](media/controlflow.PNG)

1. 用户在机器人中输入词或短语，例如，“西雅图的天气如何？”
1. CL 将用户输入传递给提取实体的机器学习模型
    - 该模型由对话学习器构建，由 www.luis.ai 托管
2. 提取的任何实体和用户输入的文本都将传递给机器人代码中的实体检测回调方法。
    - 此代码可设置/清除/操作实体值
1. 然后，CL 神经网络获取实体提取和用户输入的输出，并对机器人中定义的所有操作打分
    - 在此示例中，最可能的操作是提供天气预报：

![](media/controlflow_forecast.PNG)

5. 在这种情况下，所选操作需要执行 API 调用来检索天气预报。 
6. 随后便会调用此 API，它已使用 CL.AddAPICallback 方法进行注册。  然后，此 API 的结果会以消息形式返回给用户，例如，“晴，最高温度华氏 67°”。
7. 然后调用神经网络，以基于前一步骤确定下一操作。
8. 随后神经网络预测下一组可能的操作，并将所选操作呈现给用户，此例中会显示“还有什么需要？”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何训练对话学习器](./how-to-teach-cl.md)
