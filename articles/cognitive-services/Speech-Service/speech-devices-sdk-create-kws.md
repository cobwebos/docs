---
title: 创建自定义关键字语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终侦听关键字（或短语）。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 42bcc336bfeb325a08c3d65438d66690c0b35100
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896432"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>使用语音服务创建自定义关键字

设备始终侦听关键字（或短语）。 例如，"你好 Cortana" 是 Cortana 助手的关键字。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。

本文介绍如何为设备创建自定义关键字。

## <a name="choose-an-effective-keyword"></a>选择有效关键字

选择关键字时，请考虑以下准则：

* 关键字应为英语单词或短语。 说该词应该不超过两秒钟。

* 4 到 7 个音节的单词效果最好。 例如，"你好，Computer" 是一个不错的关键字。 而只是“Hey”则是一个糟糕的唤醒词。

* 关键字应遵循常见的英语发音规则。

* 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，"computerama" 可能是一个不错的关键字。

* 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。

* 避免使用可能包含备用发音的关键字。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。

* 不要使用特殊字符、符号或数字。 例如，"中转 #" 和 "20 + 猫" 不是好的关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择 "商标字" 一词作为关键字，请确保你拥有该商标，或者你拥有商标所有者的许可才能使用此词。 对于您选择的关键字可能产生的任何法律问题，Microsoft 不承担任何责任。

## <a name="create-your-keyword"></a>创建关键字

在可以使用自定义关键字之前，需要使用[Speech Studio](https://aka.ms/sdsdk-speechportal)上的 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页创建关键字。 提供关键字后，它会生成一个部署到设备上的文件。

1. 转到[Speech Studio](https://aka.ms/sdsdk-speechportal)并**登录**，如果还没有语音订阅，请选择 "[**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页上，创建一个**新项目**。 

1. 输入**名称**和可选**描述**，并选择语言。 每种语言都需要一个项目，并且支持目前仅限于 en-us 语言。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择你的项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要启动新的关键字模型，请单击 "**训练模型**"。

1. 为关键字模型输入**名称**和可选**说明**，并键入所选**关键字**，并单击 "**下一步**"。 我们有一些[指导原则](#choose-an-effective-keyword)来帮助选择有效的关键字。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png) 

1. 门户现在将为关键字创建候选发音。 通过单击 "播放" 按钮来倾听每个候选项，并删除任何不正确的发音旁边的检查。 仅选中好发音后，单击 "**训练**" 开始生成关键字。 

    ![查看关键字](media/custom-keyword/custom-kws-portal-choose-prons.png) 

1. 生成模型最多可能需要10分钟时间。 模型完成后，关键字列表将从**处理**更改为**成功**。 然后，你可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png) 

1. 将 .zip 文件保存到计算机。 需要此文件将自定义关键字部署到设备。

## <a name="next-steps"></a>后续步骤

通过[语音设备 SDK 快速入门](https://aka.ms/sdsdk-quickstart)测试自定义关键字。
