---
title: 对话学习器控制流 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解对话学习器控制流。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f28b60d67e84e3e2e39cc647045a6dfca473b810
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932093"
---
# <a name="control-flow"></a>控制流

本文介绍对话学习器 (CL) 的控制流，如下方图表所示。

![](media/controlflow.PNG)

1. 用户在机器人中输入词或短语，例如，“西雅图的天气如何？”
1. CL 将用户输入传递给提取实体的机器学习模型
   - 该模型由对话学习器构建，由 www.luis.ai 托管
1. 提取的任何实体和用户输入的文本都将传递给机器人代码中的实体检测回调方法。
    - 此代码可设置/清除/操作实体值
1. 然后，CL 神经网络获取实体提取和用户输入的输出，并对机器人中定义的所有操作打分
   - 在此示例中，最可能的操作是提供天气预报：

     ![](media/controlflow_forecast.PNG)

1. 在这种情况下，所选操作需要执行 API 调用来检索天气预报。 
1. 随后便会调用此 API，它已使用 CL.AddCallback 方法进行注册。  然后，此 API 的结果会以消息形式返回给用户，例如，“晴，最高温度华氏 67°”。
1. 然后调用神经网络，以基于前一步骤确定下一操作。
1. 随后神经网络预测下一组可能的操作，并将所选操作呈现给用户，此例中会显示“还有什么需要？”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何 Conversation Learner](./how-to-teach-cl.md)
